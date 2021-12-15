# High performance golang HTTP Router using Radix tree-based routing algorithm with zero memory allocation.

## Example

Benchmark func ServeHTTP:

```go
package hhttp_test

import (
	"hhttp"
	"net/http"
	"testing"
)

type route struct {
	Method  string
	Path    string
	Handler hhttp.Handler
}

func notImplemented(*hhttp.Context) {}

var routes = []route{
	{
		http.MethodGet,
		"test-dr/v1/{realmId}/{storageId}/records",
		notImplemented,
	},
	{
		http.MethodDelete,
		"test-dr/v1/{realmId}/{storageId}/records",
		notImplemented,
	},
	{
		http.MethodGet,
		"test-dr/v1/{realmId}/{storageId}/records/{recordId}",
		notImplemented,
	},
	{
		http.MethodPut,
		"test-dr/v1/{realmId}/{storageId}/records/{recordId}",
		notImplemented,
	},
	{
		http.MethodDelete,
		"test-dr/v1/{realmId}/{storageId}/records/{recordId}",
		notImplemented,
	},
	{
		http.MethodGet,
		"test-dr/v1/{realmId}/{storageId}/records/{recordId}/blocks",
		notImplemented,
	},
	{
		http.MethodDelete,
		"test-dr/v1/{realmId}/{storageId}/records/{recordId}/blocks",
		notImplemented,
	},
	{
		http.MethodGet,
		"test-dr/v1/{realmId}/{storageId}/records/{recordId}/blocks/{blockId}",
		notImplemented,
	},
	{
		http.MethodPut,
		"test-dr/v1/{realmId}/{storageId}/records/{recordId}/blocks/{blockId}",
		notImplemented,
	},
	{
		http.MethodDelete,
		"test-dr/v1/{realmId}/{storageId}/records/{recordId}/blocks/{blockId}",
		notImplemented,
	},
}

type fakeResponseWriter struct{}

func (w fakeResponseWriter) Header() http.Header {
	return nil
}

func (w fakeResponseWriter) Write([]byte) (int, error) {
	return 0, nil
}

func (w fakeResponseWriter) WriteHeader(int) {}

func BenchmarkServeHTTP(b *testing.B) {
	r := hhttp.NewRouter()
	for _, route := range routes {
		r.AddRoute(route.Method, route.Path, route.Handler)
	}
	req, _ := http.NewRequest(http.MethodGet, "http://example.com/test-dr/v1/realmId/storageId/records/recordId/blocks/blockId", nil)
	b.ReportAllocs()
	b.ResetTimer()
	b.RunParallel(func(pb *testing.PB) {
		for pb.Next() {
			r.ServeHTTP(fakeResponseWriter{}, req)
		}
	})
}
```

Run test example:

    $ go test -bench=.

Output:

```
goos: windows
goarch: amd64
pkg: hhttp
BenchmarkServeHTTP-4    11383048               105 ns/op               0 B/op          0 allocs/op
PASS
ok      hhttp   2.387s
```