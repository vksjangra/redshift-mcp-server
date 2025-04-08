# Redshift MCP Server (TypeScript)

This is a Model Context Protocol (MCP) server for Amazon Redshift implemented in TypeScript. It follows Anthropic's implementation pattern and provides Cursor IDE and other MCP-compatible clients with rich contextual information about your Redshift data warehouse. This server enables LLMs to inspect database schemas and execute read-only queries.

## Integration with Cursor

This MCP server allows Cursor to:

- Explore your Redshift database schema
- Execute read-only SQL queries
- Get table statistics and sample data
- Find columns across your data warehouse

To use this server with Cursor:

1. Start the MCP server (see Usage below)
2. In Cursor, open the Command Palette (Cmd/Ctrl + Shift + P)
3. Type "Connect to MCP Server"
4. Enter the server's stdio URL (typically `stdio://localhost`)

The Cursor AI assistant will now have access to your Redshift database schema and can help you write and execute queries.

## Prerequisites

- Node.js 16 or higher
- TypeScript
- Access to an Amazon Redshift cluster
- Basic knowledge of Redshift and SQL
- Cursor IDE installed

## Installation

1. Clone this repository or copy the files to your local system
2. Install the dependencies:

```bash
npm install
```

3. Build the TypeScript code:

```bash
npm run build
```

## Usage

The server requires a Redshift connection URL via the `DATABASE_URL` environment variable:

```bash
export DATABASE_URL="postgres://username:password@redshift-host:5439/database?sslmode=require"
npm start
```

Or you can run directly:

```bash
DATABASE_URL="postgres://username:password@redshift-host:5439/database?sslmode=require" node dist/index.js
```

For development, you can use:

```bash
DATABASE_URL="postgres://username:password@redshift-host:5439/database?sslmode=require" npm run dev
```

### Connection URL Format

```plaintext
postgres://username:password@hostname:port/database?sslmode=require
```

- **username**: Your Redshift username
- **password**: Your Redshift password
- **hostname**: Your Redshift cluster endpoint
- **port**: Usually 5439 for Redshift
- **database**: The name of your database
- **sslmode**: Set to "require" for secure connection

## Project Structure

- `src/index.ts`: Main TypeScript implementation
- `dist/`: Compiled JavaScript output
- `package.json`: Project dependencies and scripts
- `tsconfig.json`: TypeScript configuration

## Components

### Tools Available in Cursor

- **query**
  - Execute read-only SQL queries against the connected Redshift database
  - Example: "Write a query to show all tables in the public schema"

- **describe_table**
  - Get detailed information about a specific table
  - Example: "Show me the structure of the users table"

- **find_column**
  - Find tables containing columns with specific name patterns
  - Example: "Find all tables that have a column containing 'email'"

### Resources Available to Cursor

The server provides schema information that Cursor can use:

- **Schema Listings** (`redshift://<host>/schema/<schema_name>`)
  - Lists all tables within a specific schema
  - Automatically discovered from database metadata

- **Table Schemas** (`redshift://<host>/<schema>/<table>/schema`)
  - JSON schema information for each table
  - Includes column names, data types, and Redshift-specific attributes (distribution and sort keys)

- **Sample Data** (`redshift://<host>/<schema>/<table>/sample`)
  - Sample rows from each table (limited to 5)
  - Sensitive data is automatically redacted

- **Statistics** (`redshift://<host>/<schema>/<table>/statistics`)
  - Table statistics including size, row count, and creation time
  - Distribution and compression information

## Security Considerations

This server:

- Uses read-only transactions for queries to prevent modifications
- Sanitizes inputs to prevent SQL injection
- Does not expose raw password information in resource URIs
- Automatically redacts sensitive data in sample results (email, phone)
- Should be used in a secure environment since it has access to your database

## Example Cursor Interactions

Here are some example questions you can ask Cursor once connected:

1. "Show me all tables in the public schema"
2. "What's the structure of the customers table?"
3. "Find all tables that contain customer information"
4. "Write a query to count orders by status"
5. "Show me sample data from the products table"

## Extending the Server

You can extend this server by:

1. Adding new resource types in the `ListResourcesRequestSchema` and `ReadResourceRequestSchema` handlers
2. Adding new tools in the `ListToolsRequestSchema` and `CallToolRequestSchema` handlers
3. Enhancing security features or adding authentication

## Development

For development, you can use the `npm run dev` command, which uses ts-node to run the TypeScript code directly without pre-compilation.

## License

MIT
