# Notes
## Chapter 3 write a log (started 07-sept-2023)
Tried first with `/workspace/proglog/WriteALogPackage $ go test`, but got message:

```
go: github.com/gogo/protobuf@v1.3.0: missing go.sum entry for go.mod file; to add it:
        go mod download github.com/gogo/protobuf
```

Googled the msg and it seems that it is normal to run `go mod tidy` after cloning a repo, see more [here](https://go.dev/ref/mod#go-mod-tidy). And it seems to have done the trick. Executed:

```
gitpod /workspace/proglog/WriteALogPackage/internal/log (my-devel) $ go test
PASS
ok      github.com/travisjeffery/proglog/internal/log   0.006s
```

## Chapter 2 protobuf (started 31-aug-2023)
```
cd /workspace
wget https://github.com/protocolbuffers/protobuf/releases/download/v3.9.2/protoc-3.9.2-linux-x86_64.zip
unzip protoc-3.9.2-linux-x86_64.zip -d protobuf
export PATH=$PATH:/workspace/protobuf/bin
protoc --version

# go protobuf runtime
/workspace/proglog/StructureDataWithProtobuf (my-devel) $ go get google.golang.org/protobuf/...@v1.25.0 
gitpod /workspace/proglog/StructureDataWithProtobuf (my-devel) $ make
protoc api/v1/*.proto \
        --go_out=. \
        --go_opt=paths=source_relative \
        --proto_path=.
protoc-gen-go: program not found or is not executable
Please specify a program using absolute path or make sure the program is available in your PATH system variable
--go_out: protoc-gen-go: Plugin failed with status code 1.
make: *** [Makefile:2: compile] Error 1

- After some searching found out that /workspace/go did not have a bin folder
Tried execute the go get cmd in /workspace but got the message that should use go install, so:
/workspace $ go install google.golang.org/protobuf/...@v1.25.0

- That resulted in /workspace/go/bin and in there is protoc-gen-go
- Now this worked: /workspace/proglog/StructureDataWithProtobuf (my-devel) $ make

Observe, at this point need to execute export PATH=$PATH:/workspace/protobuf/bin
when gitpod workspace restarts
```
* Chapter 1 Done