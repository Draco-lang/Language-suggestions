# Application entry point

This RFC describes the entry point of application-style projects. Note, that the specification leaves out some details that will be filled in by other RFCs.

## Possible signatures

The entry point has to be a free-function named `main` (case-sensitive). The allowed parameterization/return types:
 * `func main(): unit`
 * `func main(): int32`, where the returned value is the exit code of the application
 
 The visibility of the `main` function doesn't matter.

A future RFC will add the possibility to take a string array as a parameter for the CLI arguments. This has to wait until the array type syntax is specified.

## Placement in the project

The entry point must be placed in a file `main.draco` in the root module of a package.

The rationale behind this is to introduce a convention to easily navigate to the true entry point of the application. Many C# projects already used `Program.cs` for this as a soft convention.
