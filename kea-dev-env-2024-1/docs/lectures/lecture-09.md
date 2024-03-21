# Lecture 9: Continuous Integration

In this lecture we will look at *Continuous Integration*, or CI.


## Continuous Integration

The goal of Continuous Integration (CI) is to have developers commit and integrate their work as often as possible.
We might use automation and tools for that purpose, but that is not the essential part.

By integrating small changes frequently, we hope to catch and fix errors early.

CI also mediates common problems that arise when several developers work on the same code base, e.g., that changes make the code base divert in different directions because some developers do not integrate their work over a long period of time.

Continuous integration is not a magic bullet -- great care has to be taken to avoid problems.


## So What Is The CI-Pipeline?

When we looked at *Software Quality Assurance* we worked with several tools to improve the code quality:

- Linting
- Testing
- Documenting

All these tasks can be automated and run every time someone pushes code to the code repository.


### Static Stage

Let us try to build a CI-pipeline to do just that.

!!! note inline end

    If you stick to the Gitlab conventions on naming your stages, you do not have to be as explicit as this example.
    See the documentation on Gitlab.

```yaml title=".gitlab-ci.yml"
stages:
  - static

static:
  stage: static
  image: python:3.10-alpine
  before_script:
    - pip install -r sqa-requirements.txt
    - apk update && apk add shellcheck
  script:
    - pylama .
    - djlint .
    - pip-audit
    - yamllint *.yml
    - shellcheck *.sh
```

!!! note inline end

    If any of the elements in a stage fails, that is: returns non-zero exit code, the pipeline will stop and fail all together.

In this pipeline, we define a single *stage*: the `static` stage.
As the name implies, this stage will run static tests, but also a few other things.

Gitlab CI-pipelines run on Docker containers, something we are already familiar with.
As you can see, we use the `python:3.10-alpine` Docker image for this stage.

The stage is divided into `before_script` and `script`.
The idea is that the `before_script` block takes care of setting things up, and the `script` block runs the tests themselves.

You will notice that we are using a `sqa-requirements.txt` file in this case.
This is because we do not need all the tools installed into the actual application environment to run the static stage, so we can save some time.

We then run linting on the Python source files (`pylama .`) and the HTML template files (`djlint .`).

The `pipi-audit` tool checks for packages in the environment that has security vulnerabilities. If such vulnerabilities are found, they will be reported, so we can update appropriately.

The `yamllint` tool lints `yaml` files, e.g., the `docker-compose.yml` file and the pipeline file itself.

Finally, we use `shellcheck` to test shell script files for any errors.


### Non-static Build Stage

Let us extend the pipeline with a non-static *build* stage.

```yaml title=".gitlab-ci.yml" linenums="1"
stages:
  - static
  - build

static:
  stage: static
  image: python:3.10-alpine
  before_script:
    - pip install -r sqa-requirements.txt
    - apk update && apk add shellcheck
  script:
    - pylama .
    - djlint .
    - pip-audit
    - yamllint *.yml
    - shellcheck *.sh

build:
  stage: build
  image: registry.gitlab.com/henrikstroem/composer:latest
  services:
    - docker:dind
  variables:
    DOCKER_DRIVER: overlay2
  before_script:
    - docker login -u gitlab-ci-token -p "$GITLAB_CI_TOKEN" "$CI_REGISTRY"
  script:
    - docker build --pull -t "$CI_REGISTRY_IMAGE/djangoapp:latest" .
    - RTE=test docker-compose up --abort-on-container-exit --exit-code-from app
    - docker push "$CI_REGISTRY_IMAGE/djangoapp:latest"
```

Things get a little more complicated here, so let us go through the code step=by-step.

!!! note inline end

    At the time I made the course materials, Docker's official image did not work smoothly, and this is the reason I created my own.

    This is a public project, so you can make use of the image in your project, and look into the details of how the image is made.

    You might be able to use a standard image, but this still demonstrates custom images.

In line `20` we load a *custom* Docker image.
This is a special docker image, that supports running a `docker-compose up` inside the container, so there are two levels of process isolation here.

As you can see, the custom Docker image is stored on the *container registry* of the project called *composer* belonging to my personal user account.

We must also indicate that we want to use the `dind` *Docker-in-Docker* service, which happens in line `22`.

In line `26` we do a `docker login`.
This is a login to the current project's container registry.
We make use of the pseudo-user `gitlab-ci-token` rather than our normal user, so that the project will depend on a single user and this user not changing his password.

Rather than using a regular password, we generate an API token on Gitlab, and store this token in the `GITLAB_CI_TOKEN` environment variable on Gitlab.
This is expected when we use the `gitlab-ci-token` pseudo-user.

The API token can be *masked* when we assign it to the project, so no developer can see the token in plain text after it has been configured.

Once logged in, we can access the registry.
We do a `docker build` command with the `--pull` flag in line `28`.
This pulls the image from the registry before building, so that unchanged layers can be reused.
This is for optimization reasons.
Remember the build process is running in Docker itself, so there is no local copy of the previous build.

In line `29` we run `docker-compose` on the project.
The `RTE=test` sets an environment variable `RTE` to the value `test`.
The `entrypoint` of the application image must test for the variable to know to run the tests.

There are two very important tags on this command.

The `--abort-on-container-exit` tells `docker-compose` to `down` if any container exits.
The application container should in fact exit when the tests has finished, because we do not want the `docker-compose` to keep running on the CI-pipeline.

Any other container exiting will likewise cause the `docker-compose` to `down`.

The `exit-code-from` instructs `docker-compose` to propagate the `exit code` from the `app` service to the pipeline.
This is because the tests will be running *inside* the `app` service, and thus the exit code from running the tests will be returned inside the service container.
However, we want to know if the tests fails, so this exit code value must propagate to the pipeline.

Finally, if everything has gone well, we push the new version of the app image to the project container registry in line `30`.
Remember, the pipeline will stop if at any point some command does not return a zero exit code, so if we get to this command, we know things has gone well.


## Explore The Documentation

The CI-pipeline system can do much more than we have seen in the examples above.
Explore the documentation at Gitlab to see more features.
