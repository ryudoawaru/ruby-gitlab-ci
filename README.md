Ruby GitLab CI
===

This is GitLab CI templates for Ruby and Rails project.

## Requirements

GitLab 14.3+

## Usage

Include YAML in your `.gitlab-ci.yml` and apply `variables` and `rules` to control it.

```yaml
include:
  remote: https://github.com/elct9620/ruby-gitlab-ci/raw/main/rails.yml

variables:
  RUBY_VERSION: 2.7.4
  ASSETS_PRECOMPILE: 'yes'

brakeman:
  rules:
    - if: $CI_MERGE_REQUEST_ID
```

### Webdrivers

To support E2E testing, the default `WD_INSTALL_DIR` will be configured to `tmp/webdrivers` with the cache. You can use `webdrivers` gem without extra download cost with Capybara or others which depend on `webdrivers`.

## Options

The options are usually based on the `rules` keyword to enable the task. If you overwrite the `rules` the variables are not necessary to configure.

| Type       | Environment Name        | Default   | Description                                                                            |
|------------|-------------------------|-----------|----------------------------------------------------------------------------------------|
| Ruby       | `RUBY_VERSION`          | `3.0.3`   | The ruby image version                                                                 |
| JavaScript | `NODE_PACKAGE_REQUIRED` | `yes`     | If not use Webpack the node packages are not required for Rails that can be disabled   |
| Node       | `NODE_VERSION`          | `16.13.0` | The node image version                                                                 |
| Rails      | `ASSETS_PRECOMPILE`     | Unset     | Run Rails Assets Precompile and save into artifacts                                    |
| Rails      | `RAILS_PRODUCTINO_KEY`  | Unset     | When assets precompile we may need to replace `RAILS_MASTER_KEY` to production version |
| Docker     | `DOCKER_ENABLED`        | Unset     | Run `docker build .`                                                                   |
| Docker     | `TRIVY_ENABLED`         | Unset     | Use [trivy](https://github.com/aquasecurity/trivy) to scan container                   |

### S3

Upload to AWS S3 or Minio to provide CDN for your applicatoin.

| Environment Name       | Default                                                       | Description                                                                                   |
|------------------------|---------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| `UPLOAD_TO_S3`         | Unset                                                         | When set to `yes` and `ASSETS_PRECOMPILE` is `yes` will run `assets:s3` job                   |
| `S3_ENDPOINT`          | Unset                                                         | If use Minio, set to your Minio endpoint                                                      |
| `S3_ACCESS_KEY_ID`     | Unset                                                         | If you have another `AWS_ACCESS_KEY_ID` in your tasks, use `S3_` version to overwrite it.     |
| `S3_SECRET_ACCESS_KEY` | Unset                                                         | If you have another `AWS_SECRET_ACCESS_KEY` in your tasks, use `S3_` version to overwrite it. |
| `S3_BUCKET`            | Unset                                                         | The bucket name to upload your static assets                                                  |
| `S3_SYNC_DELETE`       | 'no'                                                          | Delete remote bucket files if local source not present                                        |

## Deployment

The GitLab allows to create Review Apps when you create a merge request, we can use it for better QA flow.

### Docker Swarm

Based on [Docker Swarm Rocks](https://dockerswarm.rocks/) example, we can use [Traefik](https://dockerswarm.rocks/traefik/) and [GitLab Runner](https://dockerswarm.rocks/gitlab-ci/) runs on Docker Swarm to support Review Apps.

P.S. You have to run a GitLab CI runner in the same host with the Swarm manager and use it to deploy to the Swarm cluster.

Please reference to the `examples/review.yml` as example to configure your GitLab CI and `examples/review/docker-compose.yml` for you stack file.

### Options

| Environment Name     | Default                               | Description                                                                     |
|----------------------|---------------------------------------|---------------------------------------------------------------------------------|
| `DEPLOY_BASE_DOMAIN` | `127.0.0.1.xip.io`                    | When deploy we will use it as a base domain, e.g. `100-branch.127.0.0.1.xip.io` |
| `DEPLOY_NAME`        | `$CI_PROJECT_ID-$CI-ENVIRONMENT_SLUG` | The name used to be Docker Swarm stack name or Kubernetes namespace             |
| `DEPLOY_DOMAIN`      | `$DEPLOY_NAME.$DEPLOY_BASE_DOMAIN`    | Only work for Docker Swarm with Traefik will be set to environment url          |
| `DEPLOY_STACK_FILE`  | `docker-compose.yml`                  | The Docker Swarm stack file for deployment                                      |
| `DEPLOY_WAIT_TIME`   | `60`                                  | Time to wait for check Docker Swarm deploy status                               |

## Roadmap

* [x] Ruby support
  * [x] Rubocop
  * [x] RSpec
  * [x] Cucumber
  * [x] Bundler Audit
  * [x] Bundler Leak
* [ ] Add GitLab CI `workflow` to control jobs
* [ ] Rails support
  * [x] Brakeman
  * [x] Assets Precompile
  * [x] S3 Upload for CDN
  * [ ] Database
    * [x] PostgreSQL
    * [ ] MySQL
* [ ] JavaScript support
  * [ ] ESLint
  * [x] Yarn Audit
  * [ ] Jest
* [ ] Containerize support
  * [x] Docker
  * [x] Trivy Scanner
  * [ ] Registry
    * [x] GitLab Registry
    * [ ] AWS ECR
* [ ] Deployment
  * [x] Docker Swarm
  * [ ] Kubernetes (Not decided to add)
