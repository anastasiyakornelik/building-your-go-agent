## Rules

Scope: apply these rules to changed `.go` files whose basename does not end in
`_test.go`. Do not apply them to test files; those follow `test-conventions`.

Naming - `[LOW]`:
- Exported identifiers use `MixedCaps`; unexported identifiers use `mixedCaps`.
  No underscores and no `ALL_CAPS` constants.
- Initialisms keep one case: `ID`, `URL`, `HTTP`, `API`, `JSON`, `SQL`
  (`userID`, `ParseURL`, not `userId`, `ParseUrl`).
- Package names are short, lowercase, and have no underscores.

Doc comments - `[LOW]`:
- Every exported type, function, method, constant and variable has a doc comment.
- The comment is a full sentence that starts with the name it documents.
- Unexported identifiers do not need a doc comment.

Formatting - `[LOW]`:
- Code matches `gofmt` output: tabs for indentation, gofmt spacing and alignment.

Errors - `[MEDIUM]` for a discarded or unchecked error, `[LOW]` for wrapping and error string style:
- Never discard an error: no `_ = f()` and no `value, _ := f()`.
- Check the error right after the call and return early.
- When adding context to an error, wrap it with `fmt.Errorf("...: %w", err)` so callers can use
  `errors.Is` and `errors.As`. Do not use `%v` or `%s` for the wrapped error.
- Error strings start with a lowercase letter and have no trailing punctuation.
- The error is the last return value.

Context - `[MEDIUM]`:
- A function that does I/O or calls one that does takes `context.Context` as its
  first parameter; when the parameter is named, the name is `ctx`.
- Pass the received context on to the calls that accept one.
- Do not call `context.Background()` or `context.TODO()` where a context is
  already available, and do not store a context in a struct field.

Not applicable:
- Python rules such as `snake_case`, type hints, bare `except` or mutable default
  arguments do not apply to Go. Never report them.

Severity:
- `[MEDIUM]` - a discarded or unchecked error, a dropped or replaced context.
- `[LOW]` - naming, initialisms, doc comments, formatting, error string style.

Doc comment scope: a doc comment is required on an exported top-level
declaration (type, function, method with a receiver, constant, variable). Methods
listed inside an interface declaration and fields inside a struct do not need
their own doc comment.

## Template

Compliant:

```go
// UserStore loads users from the database.
type UserStore struct {
	db *sql.DB
}

// FindByID returns the user with the given ID.
func (s *UserStore) FindByID(ctx context.Context, userID string) (*User, error) {
	user, err := s.queryUser(ctx, userID)
	if err != nil {
		return nil, fmt.Errorf("find user %s: %w", userID, err)
	}
	return user, nil
}
```

Non-compliant:

```go
type User_Store struct {
	db *sql.DB
}

func (s *User_Store) FindById(userId string) *User {
	user, _ := s.queryUser(context.Background(), userId)
	return user
}
```

What is wrong in the non-compliant snippet:
- `User_Store` uses an underscore, and `FindById` and `userId` break the `ID`
  initialism.
- The exported type and method have no doc comments.
- The error from `queryUser` is discarded and never returned.
- The method takes no `ctx` and calls `context.Background()` instead.
