Scribe thrift interface for Golang

Example:  
```go get github.com/jakon89/scribe-thrift-go```
```go get git.apache.org/thrift.git```

main.go (adjust imports):
```golang
type Handler struct {

}

func (h *Handler) Log(messages []*scribe.LogEntry) (scribe.ResultCode, error) {
	return scribe.ResultCode_TRY_LATER, fmt.Errorf("")
}

func main() {

	handler := &Handler{}

	// always run server here
	if err := RunServer("0.0.0.0:9090", handler); err != nil {
		log.Errorf("error running server: %v", err)
	}
}

func RunServer(addr string, handler *Handler) error {
	protocolFactory := thrift.NewTBinaryProtocolFactoryDefault()

	transportFactory := thrift.NewTFramedTransportFactory(thrift.NewTTransportFactory())

	var transport thrift.TServerTransport
	var err error

	transport, err = thrift.NewTServerSocket(addr)

	if err != nil {
		return err
	}

	processor := scribe.NewScribeProcessor(handler)
	server := thrift.NewTSimpleServer4(processor, transport, transportFactory, protocolFactory)

	fmt.Println("Starting the simple server... on ", addr)
	return server.Serve()
}
```