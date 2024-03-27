# Supabase

Supabase is an open-source Firebase alternative that provides a suite of tools for building scalable web applications. This documentation serves as a comprehensive guide for developers to get started with Supabase and leverage its various features to streamline their development process.

## Introduction

Supabase offers a range of services including a PostgreSQL database, real-time database functionality, authentication services, object storage, serverless functions, and more. It aims to simplify the process of building and deploying modern web applications by providing a robust backend infrastructure that scales with your application's needs.

## Getting Started

To start using Supabase, you'll first need to [sign up for an account](https://supabase.io/) on the Supabase website. Once you've signed up, you can create a new project and get access to your Supabase dashboard where you can manage your database, authentication settings, storage, and more.

## Architecture Overview

Supabase's architecture is designed to provide developers with a robust and scalable backend infrastructure for building modern web applications. Here's an in-depth look at the key components of Supabase's architecture:

### Architecture Diagram

![Supabase Architecture](https://supabase.com/docs/_next/image?url=%2Fdocs%2Fimg%2Fsupabase-architecture.svg&w=2048&q=75)

*Diagram showing the architecture of Supabase. The Kong API gateway sits in front of 7 services: GoTrue, PostgREST, Realtime, Storage, pg_meta, Functions, and pg_graphql. All the services talk to a single Postgres instance.*

### Explanation

The architecture diagram illustrates the various components that make Supabase's backend infrastructure:

1. **Kong API Gateway**: The Kong API gateway sits in front of the Supabase services, serving as an entry point for client requests. It manages traffic, routing, security, and other API-related tasks.

2. **Services**:
   - **GoTrue**: Responsible for handling user authentication and authorization. It supports various authentication methods such as email/password, OAuth providers, and JWT tokens.
   - **PostgREST**: Generates RESTful APIs for database operations, allowing developers to perform CRUD operations on the underlying PostgreSQL database using HTTP methods.
   - **Realtime**: Provides real-time database functionality by facilitating WebSocket connections between clients and the server. Clients receive instant updates whenever there are changes to the data in the database.
   - **Storage**: Offers object storage capabilities for storing and serving files such as images, videos, and documents. It integrates with cloud storage providers for efficient file storage and retrieval.
   - **pg_meta**: Manages metadata for the PostgreSQL database, ensuring consistency and integrity of the database schema.
   - **Functions**: Allows developers to deploy serverless functions written in languages like JavaScript/Node.js. These functions extend the functionality of applications and can be used for custom business logic or integration with external services.
   - **pg_graphql**: Provides GraphQL API capabilities for interacting with the PostgreSQL database, offering a flexible and powerful query language for data retrieval and manipulation.

3. **PostgreSQL Database**: All the services interact with a single PostgreSQL instance, which serves as the primary data store for Supabase. PostgreSQL provides reliability, scalability, and rich features for storing and managing application data.

The architecture of Supabase is designed to be scalable, reliable, and feature-rich, providing developers with a comprehensive backend solution for building modern web applications.


## Conclusion

Supabase provides a comprehensive backend solution for building modern web applications, offering a range of features to streamline the development process. Whether you're building a simple prototype or a complex enterprise application, Supabase has you covered with its scalable, reliable, and easy-to-use platform.

Get started with Supabase today and take your web development projects to the next level!

