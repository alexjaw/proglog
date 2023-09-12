# Notes
## Chapter 4 Serve requests with gRPC (started 11-sept-2023)
I understand that protobuf and gRPC are important for distributed services, and I would like to get better understanding of protobuf and gRPC.

See therefore https://realpython.com/python-microservices-grpc/
### Issue make test fails for `internal/server.test`
```
gitpod /workspace/proglog/ServeRequestsWithgRPC (my-devel) $ make test
go test -race ./...
?       github.com/travisjeffery/proglog/api/v1 [no test files]
# github.com/travisjeffery/proglog/internal/server [github.com/travisjeffery/proglog/internal/server.test]
internal/server/server.go:35:6: undefined: api.RegisterLogService
internal/server/server.go:35:36: undefined: api.LogService
ok      github.com/travisjeffery/proglog/internal/log   0.043s
FAIL    github.com/travisjeffery/proglog/internal/server [build failed]
FAIL
make: *** [Makefile:12: test] Error 1
```
### Issue installing grpc 1.32
Installed with `-u` but that maybe will cause issue since it updated other packages.

Also had to use `go install instead of go get` in order to install `proto-gen-go-grpc`
```
gitpod /workspace/proglog/ServeRequestsWithgRPC (my-devel) $  go get google.golang.org/grpc@v1.32.0
go: module github.com/golang/protobuf is deprecated: Use the "google.golang.org/protobuf" module instead.

gitpod /workspace/proglog/ServeRequestsWithgRPC (my-devel) $ go get -u google.golang.org/grpc@v1.32.0
go: downloading golang.org/x/net v0.15.0
go: downloading golang.org/x/sys v0.12.0
go: downloading google.golang.org/protobuf v1.31.0
go: downloading google.golang.org/genproto/googleapis/rpc v0.0.0-20230822172742-b8732ec3820d
go: downloading golang.org/x/text v0.13.0
go: downloading github.com/google/go-cmp v0.5.5
go: module github.com/golang/protobuf is deprecated: Use the "google.golang.org/protobuf" module instead.
go: upgraded github.com/golang/protobuf v1.4.1 => v1.5.3
go: upgraded golang.org/x/net v0.0.0-20190311183353-d8887717615a => v0.15.0
go: upgraded google.golang.org/protobuf v1.25.0 => v1.31.0

gitpod /workspace/proglog/ServeRequestsWithgRPC (my-devel) $ go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.0.0
go: downloading google.golang.org/protobuf v1.23.0
gitpod /workspace/proglog/ServeRequestsWithgRPC (my-devel) $ ll /workspace/go/bin
total 42836
drwxr-xr-x 2 gitpod gitpod     135 Sep 11 05:11 ./
drwxr-xr-x 4 gitpod gitpod      28 Sep  6 04:54 ../
-rwxr-xr-x 1 gitpod gitpod 6237211 Sep  6 04:54 generate-corpus*
-rwxr-xr-x 1 gitpod gitpod 9212381 Sep  6 04:54 generate-protos*
-rwxr-xr-x 1 gitpod gitpod 4807720 Sep  6 04:54 generate-types*
-rwxr-xr-x 1 gitpod gitpod 5947682 Sep  6 04:54 pbdump*
-rwxr-xr-x 1 gitpod gitpod 8923567 Sep  6 04:54 protoc-gen-go*
-rwxr-xr-x 1 gitpod gitpod 8723909 Sep 11 05:11 protoc-gen-go-grpc*
```
### Issue Makefile
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
```

- After some searching found out that /workspace/go did not have a bin folder
Tried execute the go get cmd in /workspace but got the message that should use go install, so:
/workspace $ go install google.golang.org/protobuf/...@v1.25.0

- That resulted in /workspace/go/bin and in there is protoc-gen-go
- Now this worked: /workspace/proglog/StructureDataWithProtobuf (my-devel) $ make

Observe, at this point need to execute export PATH=$PATH:/workspace/protobuf/bin
when gitpod workspace restarts. This could be fixed with a proglog/.gitpod.yml file with content such as:
```
tasks:
  - name: Set PATH Variable
    command: echo "export PATH=$PATH:/workspace/protobuf/bin" >> $HOME/.bashrc
```
However, it seems to require a whole new image which takes ages to build. Seems much effort for suh a small thing. Instead I cn use `proglog/runme.sh` to setup the env.
