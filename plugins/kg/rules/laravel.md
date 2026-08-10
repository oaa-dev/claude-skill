# Laravel Coding Rules

Applies to Laravel 11/12 API projects. See [README.md](./README.md) for precedence — the project's
own `CLAUDE.md` always outranks this file.

## 1. Request flow

Every request passes through the full chain. Do not skip a layer, do not add one.

```
routes/api.php
  → Http/Requests/*Request        validation
  → Http/Controllers/Api/V1/*Controller   thin; constructor-injects a Service
  → Data/*Data                    spatie/laravel-data DTO, built with ::from()
  → Services/*Service             business rules, orchestration
  → Repositories/*Repository      all Eloquent access
  → Models/*
  → Http/Resources/*Resource      + Traits/ApiResponse envelope
```

**The three boundary rules:**

- Controllers never touch Eloquent or `DB`.
- Services never build queries.
- Repositories never contain business rules.

```php
// WRONG — controller reaching past the service into Eloquent
public function index(Request $request)
{
    return User::with('profile')->paginate(15);
}

// CORRECT — controller delegates; the repository owns the query
public function index(IndexUserRequest $request)
{
    return $this->successResponse($this->userService->paginate($request->perPage()));
}
```

## 2. Naming

| Thing | Case | Example |
|-------|------|---------|
| Class / file | `StudlyCase` | `UserProfileService.php` |
| Method, variable, property | `camelCase` | `findAllBy()`, `$perPage` |
| DB table, column | `snake_case` | `user_profiles`, `store_id` |
| Route path | `kebab-case` | `/api/v1/stock-transfers` |
| Config / lang key | `snake_case` | `config('services.paymongo.key')` |
| Enum case | `StudlyCase` | `AccountStatusEnum::Suspended` |
| Permission | `module.action` | `sales.void`, `products.cost` |

Never kebab-case a permission. Never camelCase a column.

## 3. Data crosses the controller→service boundary as a `Data` object

Not an array, not the `Request`.

```php
// WRONG
$this->userService->update($user, $request->validated());

// CORRECT
$this->userService->update($user, UserData::from($request));
```

Exceptions are legitimate but must be deliberate — a hot path carrying several nested collections
at once, or two scalars going into named arguments. If you make one, say so in your result.

**`Optional` means "not submitted"; an explicit `null` means "clear it".** Default every DTO field
to `new Optional` and filter on `instanceof Optional` in the service.

```php
// WRONG — a field can never be cleared
$payload = array_filter($data->all(), fn ($v) => $v !== null);

// WRONG — a PATCH of one field wipes the rest
$model->update($data->all());

// CORRECT
$payload = collect($data->all())->reject(fn ($v) => $v instanceof Optional)->all();
```

## 4. Every list endpoint uses Spatie QueryBuilder, inside the repository

Declare `allowedFilters()`, and `allowedSorts()` / `allowedIncludes()` / `defaultSort()` as needed.
`query()` assembles them and stays `protected`; index endpoints call `paginate()`.

```php
protected function allowedFilters(): array
{
    return ['name', 'status', AllowedFilter::custom('q', new GlobalSearchFilter(['name', 'category.name']))];
}
```

A filter the frontend sends but the repository does not declare is **silently ignored** and the
endpoint returns unfiltered rows. When adding a filter, add it on both sides in the same change.

## 5. Every endpoint returns a Resource through the ApiResponse envelope

Never return a model, an array, or a bare collection.

```php
// WRONG
return response()->json($user);

// CORRECT
return $this->successResponse(new UserResource($user), 'User retrieved');
```

- `successResponse` / `errorResponse` → `{success, message, data}`
- `paginatedResponse` **spreads** the paginator → `{success, message, data: [...], links, meta}`.
  Rows are at `data`, **not** `data.data`.

**Use the callback form of `whenLoaded`.** The one-argument form drops the key entirely when the
relation is loaded but null, giving an unstable response shape:

```php
// WRONG — key vanishes when the relation is loaded but empty
'role' => new RoleResource($this->whenLoaded('role')),

// CORRECT — key absent means "not loaded", null means "loaded and empty"
'role' => $this->whenLoaded('role', fn () => new RoleResource($this->role)),
```

Moving a field behind `whenLoaded()` is a two-part change: add the eager load in the same commit,
or the field silently disappears from every row with no error.

## 6. Repositories

- Every repository has an interface in `Repositories/Contracts/`, bound in
  `AppServiceProvider::$bindings`. **Services type-hint the interface, never the concrete class.**
  Any public method beyond the base must be declared on the interface.
- `update(Model $model, …)` and `delete(Model $model)` take **models, not ids** — controllers use
  route-model binding and pass the bound instance.
- `findById()` throws `ModelNotFoundException`; `find()` is the nullable one.
- Use `find($id, $columns)`, never `select($columns)->find($id)` — the latter drops the primary key
  when `$columns` omits `id`, which breaks `refresh()`.

## 7. Models

- Cast every enum-backed column on the model (`protected $casts`).
- Declare `$fillable` explicitly. Never `$guarded = []`.
- A model belonging to a tenant or branch must be scoped in its repository — a listing that
  forgets it leaks one tenant's rows to another. Treat that as a security defect, not a bug.
- Route-model binding **bypasses the repository**, so a listing scope does not protect `show` or
  `update`. Those need a Policy.

## 8. Authorization

- Permission checks live on routes (`can:` middleware), not in services. A `can()` call inside a
  service should be data scoping, not a gate — and should say so in a comment.
- **`Gate::before` must return `true` or `null`, never `false`.** Returning `false` denies every
  ability and short-circuits every Policy.

## 9. Migrations and seeders

- One migration per structural change; never edit a migration that has shipped to another
  environment.
- Composite uniques that include a tenant column must be matched by the validation rule, or the
  second tenant cannot reuse a value the database would accept.
- **Every seeder must be idempotent** (`firstOrCreate` / `updateOrCreate` / `sync`). `db:seed` is
  expected to be re-runnable.

## 10. Tests

- Pest, `tests/Feature` for endpoints. `RefreshDatabase` on feature tests only.
- Assert on the response **body**, not just the status code. A wrong-but-200 response shape is the
  failure mode this catches.
- Distinguish absent from null: `array_key_exists('role', $row)` does, `$row['role'] === null`
  does not.
- A test that cannot fail proves nothing — check that a new regression test fails against the old
  behaviour before you keep it.

## 11. Formatting

`vendor/bin/pint` before finishing. It is the authority on whitespace, import order, and brace
placement — do not hand-format against it.
