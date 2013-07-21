versioning
==========

Easy versioning for your Go binaries

:boom: **NOTE: You can use this as a library, but it's so small that you
probably shouldn't.  Instead, just copy/paste from [this gist](https://gist.github.com/rafecolton/6049826)** :boom:

This library, along with the following instructions, add the following
flags to your Go binary:

* `--rev`: prints the git revision used to build binary
* `--version`: prints the latest version, determined either by tags or
  the latest commit hash


## Using the "golang version trick"

0. Import the package into any of your `.go` files
   
   ```go
   import "github.com/rafecolton/versioning"
   ```

0. Add the command line args by adding the following to your `init()` function in the same `.go` file

    ```go
    versioning.Parse()
    ```
    
    **NOTE:** It is not required that this call be made in your `init()` function,
    but it is strongly recommended.  For it to work correctly, it must be made
    *before* your call to `flag.Parse()` (or similar), otherwise `flag` will
    explode due to unrecognized args.
    
0. Add the `ldflags` to your `Makefile`

    This step is the secret sauce that actually makes this work.  First,
    add the vars you'll need.

  ```makefile
  REV_VAR := github.com/rafecolton/versioning.RevString
  VERSION_VAR := github.com/rafecolton/versioning.VersionString
  REPO_VERSION := $(shell git describe --always --dirty --tags)
  REPO_REV := $(shell git rev-parse --sq HEAD)
  GOBUILD_VERSION_ARGS := -ldflags "-X $(REV_VAR) $(REPO_REV) -X $(VERSION_VAR) $(REPO_VERSION)"
  ```

  Then, add the args to your `go install` command.  For example,
  
  ```makefile
  go install -x $(TARGETS)
  ```
  
  becomes
  
  ```makefile
  go install $(GOBUILD_VERSION_ARGS) -x $(TARGETS)
  ```
  
0. Don't forget to include a `go get` command to pull down the required
    library.  
    
0. `make` and enjoy!
