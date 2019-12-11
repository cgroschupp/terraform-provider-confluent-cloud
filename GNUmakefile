TEST?=./...

# Go variables
GOCMD       := GO111MODULE=on go
GOFMT_FILES?=$$(find . -name '*.go' |grep -v vendor)

.PHONY: all
all: test build

.PHONY: build
build:
	$(GOCMD) install

.PHONY: test
test:
	$(GOCMD) test ./...

.PHONY: testacc
testacc:
	TF_LOG=debug TF_ACC=1 $(GOCMD) test $(TEST) -v $(TESTARGS) -timeout 120m
