## Rules

Scope: apply these rules to changed files whose basename ends in `_test.go`,
including tests colocated under `internal/`. Do not apply them to other `.go` files.

Naming - `[LOW]`:
- Test functions have the form `TestXxx(t *testing.T)`.
- The name says what behavior is tested: `TestLoadRidesReturnsRepositoryRides`,
  not `Test1`, `TestStuff` or `TestLoadRides2`.
- Cases in a table-driven test have a descriptive `name` and run with `t.Run`.

Assertions - `[MEDIUM]`:
- Check the error before using the result: fail with `t.Fatalf` when `err != nil`
  and the test cannot continue.
- Assert the observable result: the returned value, the error identity with
  `errors.Is` or `errors.As`, or the call recorded by a fake.
- A test that has no assertion, or only prints the result, is a finding.

Isolation - `[MEDIUM]`:
- No real external services: no real network hosts, databases or credentials.
  Use in-package fakes, `httptest.Server` or `t.TempDir`.
- No shared mutable package-level state between tests.
- No dependency on test order: every test sets up what it needs.
- Clean up what the test creates with `t.Cleanup` or `defer`.

Allowed and not required:
- A local `httptest.Server` and in-package fakes are allowed. A local
  `httptest` server is not an external service.
- Do not require doc comments on test functions or unexported test helpers.
- Python rules such as `snake_case` test names or type hints do not apply to Go.

## Template

Compliant:

```go
func TestConvertReturnsRateFromSource(t *testing.T) {
	source := fakeRateSource{rate: 1.25}
	converter := NewConverter(source)

	got, err := converter.Convert(context.Background(), 100)
	if err != nil {
		t.Fatalf("Convert() error = %v", err)
	}
	if want := 125.0; got != want {
		t.Errorf("Convert() = %v, want %v", got, want)
	}
}
```

Non-compliant:

```go
var sharedClient = &http.Client{}

func Test1(t *testing.T) {
	resp, _ := sharedClient.Get("https://api.example.com/rates")
	body, _ := io.ReadAll(resp.Body)
	fmt.Println(string(body))
}
```

What is wrong in the non-compliant test:
- `Test1` does not say what behavior is tested.
- Both errors are discarded, and `resp` is used before any error check.
- The test only prints the body and asserts nothing.
- It calls a real external host and relies on a shared package-level client.
- The response body is never closed or cleaned up.
