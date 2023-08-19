## Examples for using the khulnasoft/khulnasoft-engine@1.8 CircleCi orb:

Adding a public image scan job to a CircleCi workflow:
```
version: 2.1
orbs:
  khulnasoft: khulnasoft/khulnasoft-engine@1
workflows:
  scan_image:
    jobs:
      - khulnasoft/image_scan:
          image_name: khulnasoft/khulnasoft-engine:latest
          timeout: '300'
```

Adding a private image scan job to a CircleCi workflow:
```
version: 2.1
orbs:
  khulnasoft: khulnasoft/khulnasoft-engine@1
workflows:
  scan_image:
    jobs:
      - khulnasoft/image_scan:
          image_name: khulnasoft/khulnasoft-engine:latest
          timeout: '300'
          private_registry: True
          registry_name: docker.io
          registry_user: "${DOCKER_USER}"
          registry_pass: "${DOCKER_PASS}"
```
Adding image scanning to your container build pipeline job.
```
version: 2.1
orbs:
  khulnasoft: khulnasoft/khulnasoft-engine@1
jobs:
  local_image_scan:
    executor: khulnasoft/khulnasoft_engine
    steps:
      - setup_remote_docker
      - checkout
      - run:
          name: build container
          command: docker build -t "khulnasoft/khulnasoft-engine:ci" .
      - khulnasoft/analyze_local_image:
          image_name: example/test:latest
          timeout: '500'
          dockerfile_path: ./Dockerfile
      - khulnasoft/parse_reports
      - store_artifacts:
          path: khulnasoft-reports
```

Put a custom policy bundle in to your repo at .circleci/.khulnasoft/policy_bundle.json
Job will be marked as 'failed' if the KhulnaSoft policy evaluation gives 'fail' status
```
version: 2.1
orbs:
  khulnasoft: khulnasoft/khulnasoft-engine@1
jobs:
  local_image_scan:
    executor: khulnasoft/khulnasoft_engine
    steps:
      - checkout:
          path: ~/project/src/
      - run:
          name: build container
          command: docker build -t ${CIRCLE_PROJECT_REPONAME}:ci ~/project/src/
      - khulnasoft/analyze_local_image:
          image_name: ${CIRCLE_PROJECT_REPONAME}:ci
          timeout: '500'
          policy_failure: True
          policy_bundle_file_path: .circleci/.khulnasoft/policy_bundle.json
          dockerfile_path: ./Dockerfile
      - khulnasoft/parse_reports
      - store_artifacts:
          path: khulnasoft-reports
```

Build and scan multiple images, using a custom policy bundle.
```
version: 2.1
orbs:
  khulnasoft: khulnasoft/khulnasoft-engine@1
jobs:
  local_image_scan:
    executor: khulnasoft/khulnasoft_engine
    steps:
      - setup_remote_docker
      - checkout
      - run:
          name: build containers
          command: |
            docker build -t "example/test:dev" dev/
            docker build -t "example/test:staging" staging/
            docker build -t "example/test:latest" prod/
      - khulnasoft/analyze_local_image:
          image_name: "example/test:dev example/test:staging example/test:latest"
          timeout: '500'
          policy_failure: True
          policy_bundle_file_path: .circleci/.khulnasoft/policy_bundle.json
      - khulnasoft/parse_reports
      - store_artifacts:
          path: khulnasoft-reports
```
