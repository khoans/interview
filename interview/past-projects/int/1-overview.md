# Project Overview

The project is an ecommerce platform for a Korean client, selling furniture, home decor products, and interior design consulting services. The platform's standout feature is an AI-powered product visualization engine — users upload a photo of their room, and the system automatically generates a realistic image showing how selected products from our inventory would look in that space, helping users make better purchasing decisions while promoting in-stock products.

The system consists of two main components: the ecommerce platform and the AI rendering engine.

On the backend, we use Java with Spring Boot to provide REST APIs for the storefront, admin panel, and AI integration. The frontend is built with Next.js, leveraging server-side rendering for SEO in the Korean market. We use MariaDB as the database for products, orders, users, and all relational data.

On the AI side, the rendering engine is a separate service built with FlaskAPI and Google Nano Banana, deployed on a dedicated EC2 instance. It exposes REST APIs that the Spring Boot backend consumes to generate product visualization images.

The platform also includes an admin panel for managing products, orders, users, and promotional content.

For deployment, the main application runs on a single EC2 instance with a blue-green deployment strategy via Bitbucket Pipelines. Since the platform has a modest concurrent user count, a single instance is sufficient for the current scale.

The project currently serves the Korean market. An international version targeting the US market also exists, built with Node.js on the backend and integrated with Shopify as the ecommerce platform. We will be handling the international version in the future.

For data storage, we use MariaDB as the primary database and S3 for product images, user-uploaded room photos, and AI-generated visualization images.
