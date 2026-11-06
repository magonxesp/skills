---
name: openapi-documentation
description: Create and maintain OpenAPI 3.0 documentation for REST APIs from existing route/controller code and tests across frameworks (Actix Web, Express, Spring Boot, and similar). Use when a project needs an `api.yaml` spec written, completed, or updated to match current endpoints, request/response schemas, status codes, and examples. Typical prompts include: "Crea la documentacion de la api rest en OpenAPI de todos los controladores", "Crea la documentacion de la api rest en OpenAPI de todas las rutas", "Documenta la ruta /api/user en OpenAPI", and "Actualiza la documentacion de la ruta /api/user en OpenAPI".
---

# OpenAPI Documentation

Document the project's REST API in OpenAPI 3.0 using source-of-truth code and tests.

## Workflow

1. Detect the API framework and route locations.
2. Discover endpoints from controllers/routes.
3. Read route/controller tests when available to infer real behavior.
4. Build or update OpenAPI 3.0 paths, parameters, request bodies, responses, and examples.
5. Validate internal consistency before finishing.

## Locate Spec File

1. Check whether `api.yaml` exists at project root.
2. If it exists, update it in place.
3. If it does not exist, ask the user where the OpenAPI file should live or whether to create a new root `api.yaml`.
4. Do not create a new spec file path without explicit user confirmation.

## Extract Endpoints

1. Map each handler to method + path.
2. Include path params, query params, headers, and request body.
3. Infer media types from code and tests (for example `application/json`, `multipart/form-data`, `application/octet-stream`).
4. Preserve versioning and route groups exactly as implemented.

Framework hints:
- Actix Web (Rust): inspect `App::service`, `scope`, route macros (`#[get]`, `#[post]`, etc.), and integration tests.
- Express (Node): inspect router files (`router.get/post/...`), mounted prefixes, middleware, and request tests.
- Spring Boot (Java/Kotlin): inspect `@RequestMapping`, method annotations, DTOs, validation annotations, and controller tests.

## Use Tests as Behavior Evidence

1. Prioritize tests to document actual status codes and payload shapes.
2. Derive positive and negative responses from test cases.
3. Infer required vs optional fields from assertions and builder usage.
4. If behavior is ambiguous in code and tests, document conservative defaults and leave a clear TODO note in the spec.

## Author OpenAPI 3.0

1. Keep `openapi` version in the 3.0.x line.
2. Populate minimum top-level sections: `openapi`, `info`, `paths`.
3. Use `components/schemas` for reusable models.
4. Reuse shared response schemas and error models.
5. Add `tags` grouped by domain/resource when meaningful.
6. Ensure every operation has at least one response.

## Quality Checks

1. Confirm each documented endpoint exists in code.
2. Confirm each discovered route is represented in spec.
3. Check path parameter names match exactly.
4. Check request/response schema types are consistent with handlers/tests.
5. Avoid deleting existing valid sections unrelated to touched endpoints.

## Update Strategy

1. Prefer surgical edits over full-file rewrites.
2. Preserve ordering and style where possible.
3. Keep backward compatibility unless code clearly changed.
4. When code changes imply breaking API changes, call them out explicitly.
