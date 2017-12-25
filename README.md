# The Three Musketeers

## Guideline

> The code examples in this guideline can be found in `examples/golang-serverless`

### Makefile

#### Names target and _target

This is just a naming convention. `target` is meant to be executed with docker and docker compose whereas `_target` inside a container.

`_target` can also be executed on the computer if the environment satisfies the target requirements. For instance, you can have a Go development environment setup locally and run `$ make _test`.

```Makefile
test: $(DOTENV_TARGET) $(GOLANG_DEPS_DIR)
  docker-compose run --rm goshim make _test
.PHONY: test

_test:
  go test -v
.PHONY: _test
```

#### Target dependencies

To make the Makefile easier to read avoid having many target dependencies: `target: a b c`. Restrict the dependencies only to `target` and not `_target`

#### Pipeline targets

Pipeline targets are targets being executed on the CI server. A typical pipeline targets would have `deps, test, build, deploy`.

It is best having them at the top of the Makefile as it gives an idea of the project pipeline and where to start when the project is being downloaded.

#### Project dependencies

It is a good thing to have a target `deps` to install all the dependencies required to test/build/deploy the project.

Create zip artifact(s) like `golang_vendor.zip` so that the CI can carry it across tasks. Use this artifact(s) to be a dependency to other targets so if it does not exist, the targets will fail.

```Makefile
deps: $(DOTENV_TARGET)
  docker-compose run --rm goshim make _depsGo
.PHONY: deps

test: $(DOTENV_TARGET) $(GOLANG_DEPS_DIR)
	docker-compose run --rm goshim make _test
.PHONY: test

$(GOLANG_DEPS_DIR): $(GOLANG_DEPS_ARTIFACT)
  unzip -qo -d . $(GOLANG_DEPS_ARTIFACT)

_depsGo:
  dep ensure
  zip -rq $(GOLANG_DEPS_ARTIFACT) $(GOLANG_DEPS_DIR)/
.PHONY: _depsGo
```

### .env

#### .env.template



#### .env.example

Very useful when starting on a project to have a `.env.example` which has defined values so that you can for instance run the test straight away with `$ make test DOTENV=.env.example`.

### Docker Compose
