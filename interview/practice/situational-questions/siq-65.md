**65.** How did you handle data validation? Where do you validate — frontend, backend, or both?

========== THIS SECTION IS THE ANSWER ==========

Both, but with different purposes. Frontend validation is for user experience; backend validation is for security and data integrity. Never trust the frontend alone.

On the automotive marketplace, the Next.js frontend validated inputs for immediate user feedback — required fields, format checks (email, phone), price range sanity (min < max). On the backend, every Spring Boot controller used Bean Validation annotations (@NotNull, @Size, @Min, @Max, @Pattern) on request DTOs, enforced via @Valid. For complex business rules — like "a dealer can't list more than 500 vehicles" or "listing price must be within 50% of KBB estimated value" — we validated in the service layer with custom validators that threw specific exceptions mapped to meaningful error responses. We also validated at the database level with PostgreSQL constraints (NOT NULL, CHECK constraints, foreign keys) as the last line of defense.

The trade-off: triple validation (frontend, backend, database) means maintaining rules in three places. We mitigated this by sharing validation schemas — the OpenAPI spec defined constraints that both frontend and backend could reference. The lesson: the backend is the only validation layer you can trust. I've seen bugs where frontend validation was bypassed by disabling JavaScript or calling the API directly with Postman.
