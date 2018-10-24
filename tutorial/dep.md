# Building your app

## `Gopkg.toml`

Golang has a few dependency management tools. In this tutorial you will be using [`dep`](https://golang.github.io/dep/). `dep` uses a `Gopkg.toml` file in the root of the repository to define what dependencies the application needs. Cosmos SDK apps currently depend on specific versions of some libraries. The below manifest contains all the necessary versions. Create the `./Gopkg.toml` file and paste the `toml` below in the file:

```toml
# Gopkg.toml example
#
# Refer to https://github.com/golang/dep/blob/master/docs/Gopkg.toml.md
# for detailed Gopkg.toml documentation.
#
# required = ["github.com/user/thing/cmd/thing"]
# ignored = ["github.com/user/project/pkgX", "bitbucket.org/user/project/pkgA/pkgY"]
#
# [[constraint]]
#   name = "github.com/user/project"
#   version = "1.0.0"
#
# [[override]]
#   name = "github.com/x/y"
#   version = "2.4.0"
#
# [prune]
#   non-go = false
#   go-tests = true
#   unused-packages = true

[[constraint]]
  name = "github.com/cosmos/cosmos-sdk"
  branch = "develop"

[[override]]
  name = "github.com/golang/protobuf"
  version = "=1.1.0"

[[constraint]]
  name = "github.com/spf13/cobra"
  version = "~0.0.1"

[[constraint]]
  name = "github.com/spf13/viper"
  version = "~1.0.0"

[[override]]
  name = "github.com/tendermint/go-amino"
  version = "=v0.12.0"

[[override]]
  name = "github.com/tendermint/iavl"
  version = "=v0.11.0"

[[override]]
  name = "github.com/tendermint/tendermint"
  version = "=0.25.0"

[[override]]
  name = "golang.org/x/crypto"
  source = "https://github.com/tendermint/crypto"
  revision = "3764759f34a542a3aef74d6b02e35be7ab893bba"

[prune]
  go-tests = true
  unused-packages = true
```

### Makefile

Finish your application by writing the `Makefile` to help make common/complex build and test commands easy to run:

> _*NOTE*_: The below Makefile contains some of same commands as the Cosmos SDK and Tendermint Makefiles.

```
get_tools:
ifdef DEP_CHECK
    @echo "Dep is already installed.  Run 'make update_tools' to update."
else
    @echo "Installing dep"
    go get -v $(DEP)
endif
ifdef STATIK_CHECK
    @echo "Statik is already installed.  Run 'make update_tools' to update."
else
    @echo "Installing statik"
    go version
    go get -v $(STATIK)
endif

get_vendor_deps:
    @echo "--> Generating vendor directory via dep ensure"
    @rm -rf .vendor-new
    @dep ensure -v -vendor-only

update_vendor_deps:
  @echo "--> Running dep ensure"
  @rm -rf .vendor-new
  @dep ensure -v -update

install:
    go install ./cmd/nameshaked
    go install ./cmd/nameshakecli
```

## Building the app

First, you need to install `dep`. Below there is a command for using a shell script from `dep`'s site to preform this install.

```bash
# Install dep
make get_tools

# Initialize dep and install dependencies
dep init
make get_vendor_deps

# Install the app into your $GOBIN
make install

# Now you should be able to run the following commands:
nameserviced help
nameservicecli help
```

### Congratulations, you have finished your nameservice application! Try [running and interacting with it](../README.md#running-the-live-network-and-using-the-commands)!