TEST?=./...

# Project variables
NAME        := terraform-provider-confluentcloud
# Build variables
BUILD_DIR   := bin
VERSION     ?= $(shell git describe --tags --exact-match 2>/dev/null || git describe --tags 2>/dev/null || echo "v0.0.0-$(COMMIT_HASH)")
# Go variables
GOCMD       := GO111MODULE=on go
GOBUILD     ?= CGO_ENABLED=0 $(GOCMD) build
GOOS        ?= $(shell go env GOOS)
GOARCH      ?= $(shell go env GOARCH)
GOFILES     ?= $(shell find . -type f -name '*.go' -not -path "./vendor/*")

.PHONY: all
all: clean test build


.PHONY: checkfmt
checkfmt: RESULT = $(shell goimports -l $(GOFILES) | tee >(if [ "$$(wc -l)" = 0 ]; then echo "OK"; fi))
checkfmt: SHELL := /usr/bin/env bash
checkfmt: ## Check formatting of all go files
	@ echo "$(RESULT)"
	@ if [ "$(RESULT)" != "OK" ]; then exit 1; fi

.PHONY: clean
clean: ## Clean workspace
	#@ $(MAKE) --no-print-directory log-$@
	rm -rf ./$(BUILD_DIR)

.PHONY: build
build: clean ## Build binary for current OS/ARCH
	$(GOBUILD) -o ./$(BUILD_DIR)/$(GOOS)-$(GOARCH)/$(NAME)

.PHONY: build-all
build-all: GOOS      = linux darwin
build-all: GOARCH    = amd64
build-all: clean ## Build binary for all OS/ARCH
	@ ./scripts/build/build-all-osarch.sh "$(BUILD_DIR)" "$(NAME)" "$(VERSION)" "$(GOOS)" "$(GOARCH)"

.PHONY: test
test:
	$(GOCMD) test ./...

.PHONY: testacc
testacc:
	TF_LOG=debug TF_ACC=1 $(GOCMD) test $(TEST) -v $(TESTARGS) -timeout 120m

.PHONY: gox
gox:
	GO111MODULE=off go get -u github.com/mitchellh/gox