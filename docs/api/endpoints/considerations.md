# Introduction

This entry serves as notes you have to keep in mind to follow up seamlessly with the things explained in the Endpoints documentation.

## JSON 

The API uses JSON (JavaScript Object Notation) as the standard format for both requests and responses. JSON is lightweight, easy to read, and widely supported across different programming languages and frameworks, making it ideal for modern APIs.

## Testing the API 

To test the API endpoints, you can use tools like Postman or any other HTTP client of your choice. These tools allow you to send requests, inspect responses, and debug your interactions with the API.

**Recommended Tool: Postman**

While there are several options available for testing APIs, Postman is the recommended tool for the following reasons:

1. **Widely Used:** Postman is one of the most popular API testing tools and is trusted by developers worldwide.
2. **Team Standard:** Our development team uses Postman as the primary tool for API testing, ensuring consistency and better collaboration.
3. **Ease of Use:** Postman provides a user-friendly interface, advanced features like automated testing, and support for importing/exporting collections.
4. **Environment Variables:** Postman allows you to define and manage environments (e.g., dev, staging, production) for seamless testing.

### Additional Notes

- If you prefer alternatives to Postman, tools like Insomnia, cURL, or HTTPie can also be used.

## API Authorization

### Public Routes

Any endpoint under the `/auth/**` pattern is accessible to all users, regardless of whether they are authenticated.

Examples of endpoints in this category:

- `/auth/login`
- `/auth/register`
- `/auth/logout`

These routes are used for authentication or registration purposes, so they do not require prior authentication.

### Protected Routes (All Other Requests)

Any endpoint that does not match the `/auth/**` pattern requires authentication.

If a user tries to access these endpoints without being authenticated, they will receive a `401 Unauthorized` error.