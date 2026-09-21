# Nuxt AI

## Nuxt MCP Server

Sử dụng tài liệu Nuxt trong AI assistants với Model Context Protocol support.

## MCP là gì?

MCP (Model Context Protocol) là một protocol tiêu chuẩn cho phép AI assistants truy cập các data sources và tools bên ngoài. Nuxt cung cấp một MCP server cho phép AI assistants như Claude Code, Cursor và Windsurf truy cập tài liệu, blog posts và deployment guides trực tiếp.

## Resources

MCP server cung cấp các resources sau:
- **resource://nuxt-com/documentation-pages**: Browse tất cả documentation pages
- **resource://nuxt-com/blog-posts**: Browse tất cả Nuxt blog posts
- **resource://nuxt-com/deploy-providers**: Browse tất cả deployment providers

## Tools

### Documentation
- **list-documentation-pages**: Liệt kê tất cả documentation pages
- **get-documentation-page**: Lấy nội dung documentation page
- **get-getting-started-guide**: Lấy getting started guide

### Blog
- **list-blog-posts**: Liệt kê tất cả blog posts
- **get-blog-post**: Lấy blog post content

### Deployment
- **list-deploy-providers**: Liệt kê deployment providers
- **get-deploy-provider**: Lấy deployment instructions

### Modules
- **list-modules**: Liệt kê Nuxt modules
- **get-module**: Lấy chi tiết module

### Changelog
- **get-changelog**: Lấy releases mới nhất

## Setup

### Claude Code

```bash
claude mcp add --transport http nuxt https://nuxt.com/mcp
```

### Claude Desktop

```json
{
  "mcpServers": {
    "nuxt": {
      "command": "npx",
      "args": ["mcp-remote", "https://nuxt.com/mcp"]
    }
  }
}
```

### Cursor

```json
{
  "mcpServers": {
    "nuxt": {
      "type": "http",
      "url": "https://nuxt.com/mcp"
    }
  }
}
```

### Visual Studio Code

```json
{
  "servers": {
    "nuxt": {
      "type": "http",
      "url": "https://nuxt.com/mcp"
    }
  }
}
```

### GitHub Copilot

```json
{
  "mcpServers": {
    "nuxt": {
      "type": "http",
      "url": "https://nuxt.com/mcp",
      "tools": ["*"]
    }
  }
}
```

## Prompts

Sau khi cấu hình, bạn có thể hỏi AI assistant:
- "List all available Nuxt documentation pages"
- "What's the difference between v3 and v4?"
- "How do I deploy to Vercel?"
- "Show me the latest blog posts"
