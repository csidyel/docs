* [Supported Versions](#supported-versions)
* [GOPATH](#gopath)
* [Dependency Caching](#dependency-caching)
* [System Dependencies](#c-system-dependencies)

## Supported Versions

Go 1.10 is the default version. Go 1.11 is supported as well. You can
change versions with `sem-version`. Here's an example:

<pre><code class="language-yaml">blocks:
  - name: Tests
    task:
      prologue:
        commands:
          - sem-version go 1.11
      jobs:
        - name: Tests
          commands:
            - go version
</code></pre>

## GOPATH

If you're not using Go 1.11 then you'll need to prepare the directory
structure Go tooling expects. This requires creating `$GOPATH/src` and
cloning your code into the correct directory. Luckily this is possible
by changing some environment variables and using the existing
Semaphore toolbox.

<pre><code class="language-yaml">
version: v1.0
name: Go Example
agent:
  machine:
    type: e1-standard-2
    os_image: ubuntu1804

blocks:
  - name: "Test"
    task:
      prologue:
        commands:
          - export "SEMAPHORE_GIT_DIR=$(go env GOPATH)/src/github.com/${SEMAPHORE_PROJECT_NAME}"
          - export "PATH=$(go env GOPATH)/bin:${PATH}"
          - mkdir -vp "${SEMAPHORE_GIT_DIR}" "$(go env GOPATH)/bin"
      jobs:
        - name: Test Suite
          commands:
            # Uses the redefined SEMAPHORE_GIT_DIR to clone code into the correct directory
            - checkout
            # Further setup from this point on
</code></pre>

## Dependency Caching

Go projects use multiple approaches to dependency management. If
you're using `dep` then strategy is similar to other projects. Use the
`cache` util to store and restore `vendor`.

<pre><code class="language-yaml">
version: v1.0
name: Go Example
agent:
  machine:
    type: e1-standard-2
    os_image: ubuntu1804

blocks:
  - name: Warm cache
    task:
      prologue:
        commands:
          # Go project boiler plate
          - export "SEMAPHORE_GIT_DIR=$(go env GOPATH)/src/github.com/${SEMAPHORE_PROJECT_NAME}"
          - export "PATH=$(go env GOPATH)/bin:${PATH}"
          - mkdir -vp "${SEMAPHORE_GIT_DIR}" "$(go env GOPATH)/bin"
          # Dep install db
          - curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh
          - checkout
      jobs:
        - name:
          commands:
            - cache restore v1-deps-$(checksum Gopkg.lock)
            - dep ensure -v
            - cache store v1-deps-$(checksum Gopkg.lock) vendor
  - name: Tests
      prologue:
        commands:
          # Go project boiler plate
          - export "SEMAPHORE_GIT_DIR=$(go env GOPATH)/src/github.com/${SEMAPHORE_PROJECT_NAME}"
          - export "PATH=$(go env GOPATH)/bin:${PATH}"
          - mkdir -vp "${SEMAPHORE_GIT_DIR}" "$(go env GOPATH)/bin"
          # Dep install db
          - curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh
          - checkout
          - cache restore v1-deps-$(checksum Gopkg.lock)
      jobs:
        - name: Suite
          commands:
            - make test
</code></pre>

## System Dependencies

You have full `sudo` access if you need to install a system package.
Here's an example:

<pre><code class="language-yaml">
blocks:
  - name: Tests
    task:
      prologue:
        commands:
          - sudo apt-get update && sudo apt-get install -y libpq-dev
      jobs:
        - name: Everything
          commands:
            - make test
</code></pre>