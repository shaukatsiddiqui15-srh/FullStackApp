# InventoryHub

A small full-stack inventory management starter. A Blazor WebAssembly client (`ClientApp`) calls a .NET Minimal API back-end (`ServerApp`) to display product data.

## Projects

- **`ServerApp/`** — .NET Minimal API exposing `GET /api/products`.
- **`ClientApp/`** — Blazor WebAssembly front-end with a `Products` page that fetches and renders the list.

## Prerequisites

- .NET 9 SDK

## Running locally

Run each project in its own terminal.

```bash
# Terminal 1 — back-end API
cd ServerApp
dotnet run
# listening on http://localhost:5032 (and https://localhost:7136)
```

```bash
# Terminal 2 — Blazor client
cd ClientApp
dotnet run
# listening on http://localhost:5179 (and https://localhost:7192)
```

Open the client URL, then click **Products** in the nav menu. You should see:

```
Laptop - $1200.5 (Stock: 25)
Headphones - $50 (Stock: 100)
```

## How the integration works

### Back-end — `ServerApp/Program.cs`

- Defines a CORS policy `AllowBlazorClient` allowing the Blazor client origins (`http://localhost:5179`, `https://localhost:7192`).
- Maps `GET /api/products` returning an array of `{ Id, Name, Price, Stock }`.

### Front-end — `ClientApp/Program.cs`

- Registers a singleton-style scoped `HttpClient` with `BaseAddress = http://localhost:5032` so calls like `GetFromJsonAsync("/api/products")` reach the API.

### Front-end — `ClientApp/Pages/FetchProducts.razor`

- Injects `HttpClient` and fetches the product list in `OnInitializedAsync`.
- Handles three render states: loading, empty, error.
- Catches `HttpRequestException` (network / CORS failures) and `JsonException` (malformed payload) so the UI never crashes silently.

### Front-end — `ClientApp/Layout/NavMenu.razor`

- Adds a **Products** nav link routing to `/fetchproducts`.

## Configuration notes

- The API base URL is hardcoded in `ClientApp/Program.cs`. For a real deployment, move it to `appsettings.json` and read via `builder.Configuration`.
- If you launch the API over HTTPS only, update both the client `BaseAddress` and the CORS allow-list to match — mixing schemes across origins will fail in the browser.
