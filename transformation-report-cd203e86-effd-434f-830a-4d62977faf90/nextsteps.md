# Next Steps

## Summary

The transformation appears to have completed successfully. No build errors were detected across any of the projects in the solution:

- `Bookstore.Data`
- `Bookstore.Domain.Tests`
- `Bookstore.Cdk`
- `Bookstore.Web`
- `Bookstore.Domain`

The following steps outline how to validate, test, and deploy the migrated solution.

---

## 1. Restore Dependencies

Run a full NuGet package restore to ensure all dependencies are resolved correctly in the new .NET environment:

```bash
dotnet restore
```

Review the output for any warnings related to package compatibility or version conflicts. Address any packages that may have been marked as deprecated or that target older frameworks.

---

## 2. Build the Solution

Perform a full solution build to confirm there are no compilation issues:

```bash
dotnet build --configuration Release
```

Ensure the build completes with zero errors and review any warnings, particularly those related to nullable reference types, obsolete APIs, or platform compatibility.

---

## 3. Run Unit Tests

Execute the test project to validate that existing business logic behaves as expected after migration:

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release --verbosity normal
```

Review the test results carefully. Any failing tests should be investigated to determine whether they indicate a regression introduced during migration or a pre-existing issue.

---

## 4. Validate the Web Application Locally

Run the web application locally to verify runtime behavior:

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj --configuration Release
```

Manually verify the following:

- Application starts without runtime exceptions
- All pages and routes load correctly
- Database connectivity is functioning (if applicable via `Bookstore.Data`)
- Any authentication or authorization flows work as expected

---

## 5. Verify Data Layer

If `Bookstore.Data` uses Entity Framework Core or another ORM, confirm that:

- The connection strings are correctly configured for the new environment
- Any pending migrations are applied:

```bash
dotnet ef database update --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

- Data access operations (CRUD) function correctly against the target database

---

## 6. Review CDK Project

The `Bookstore.Cdk` project likely defines infrastructure. Review it to ensure:

- Any SDK or library references (e.g., AWS CDK) are updated to versions compatible with the new .NET target framework
- Configuration values such as environment names, regions, or resource identifiers are accurate for the target deployment environment

Build the CDK project independently to confirm it compiles cleanly:

```bash
dotnet build app/Bookstore.Cdk/Bookstore.Cdk.csproj --configuration Release
```

---

## 7. Check Target Framework Consistency

Open each `.csproj` file and confirm that all projects are targeting the same intended .NET version (e.g., `net8.0`). Inconsistent target frameworks across projects can cause subtle runtime issues even when the build succeeds.

---

## 8. Review Configuration Files

Check `appsettings.json` and any environment-specific configuration files (`appsettings.Development.json`, `appsettings.Production.json`) to ensure:

- Connection strings are updated
- Any legacy configuration keys specific to .NET Framework (e.g., `system.web`, `appSettings` in `web.config`) have been migrated to the .NET `appsettings.json` format
- Logging configuration is correct

---

## 9. Deploy to Target Environment

Once all local validation steps pass, deploy the application to the target environment:

```bash
dotnet publish app/Bookstore.Web/Bookstore.Web.csproj --configuration Release --output ./publish
```

Copy the contents of the `./publish` directory to the target server or hosting environment and verify the application starts and operates correctly in that environment.