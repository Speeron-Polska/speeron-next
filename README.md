# Speeron NEXT

**Speeron NEXT** is a comprehensive digital guest journey platform that transforms hotel operations through seamless technology integration. The platform revolutionizes how hotels interact with guests by providing end-to-end digital solutions that streamline operations and enhance the guest experience.

## Core Capabilities

Speeron NEXT enables hotels to deliver personalized, automated guest experiences throughout the entire guest lifecycle:

- **Pre-Arrival Services**: Automated check-in link generation, booking verification, and guest communication
- **Digital Check-In**: Self-service check-in processes accessible via web, mobile, and kiosk interfaces
- **In-Stay Services**: Guest portal functionality, room service ordering, concierge services, and real-time communication
- **Check-Out**: Streamlined checkout processes with digital invoicing and payment processing
- **Multi-Channel Access**: Web applications, mobile interfaces, kiosk terminals, and TV-based guest portals

## Key Features

### Multi-Tenant Architecture
The platform supports multiple hotel properties with intelligent tenant resolution, allowing seamless operations across hotel chains and independent properties. Each hotel maintains its own configuration while benefiting from shared infrastructure.

### PMS Integration
Deep integration with Property Management Systems (PMS), enabling real-time synchronization of bookings, guest data, and room availability. The platform supports various PMS adapters for flexible hotel system connectivity.

### Payment Processing
Comprehensive payment solutions supporting multiple payment methods, including credit cards, digital wallets, and online payment gateways. Secure transaction processing with PCI compliance considerations.

### Guest Communication
Automated messaging and communication tools that keep guests informed throughout their stay, from booking confirmation to post-stay follow-up.

### Analytics and Reporting
Built-in analytics capabilities that provide hotels with insights into guest behavior, operational efficiency, and service utilization patterns.

## Technology Stack

Built on modern .NET technologies with a modular architecture that supports:
- Scalable cloud-based deployment
- RESTful API integrations
- Real-time data synchronization
- Multi-language and multi-currency support
- Responsive web and mobile interfaces

## Value Proposition

Speeron NEXT empowers hotels to:
- **Reduce Operational Costs**: Automate routine tasks and reduce front desk workload
- **Enhance Guest Satisfaction**: Provide convenient, 24/7 self-service options
- **Increase Revenue**: Enable upselling opportunities and streamline payment processing
- **Improve Efficiency**: Integrate seamlessly with existing hotel systems and workflows
- **Scale Operations**: Support properties of all sizes with flexible, cloud-based infrastructure

The platform serves as the foundation for Speeron's digital hospitality ecosystem, connecting guests with hotel services through intuitive, technology-driven interfaces that modernize the traditional hotel experience.

# Speeron NEXT MCP Server - Official Claude Connector Documentation

## Description

The Speeron NEXT MCP server provides hotel check-in and room booking services through natural language interactions. It offers automated check-in link generation by searching bookings with booking ID and guest last name, supporting multi-tenant hotel properties with intelligent tenant resolution. The server also provides room availability search with multiple pricing options and booking confirmation capabilities, seamlessly integrating hotel operations into conversational experiences.

## 1. Tool Descriptions

### 1.1 check_in_user (Hotel Check-In Link Retrieval)

**What it does:** Helps guests retrieve their personalized check-in link by searching for their booking using booking ID and guest last name. The tool automatically resolves the hotel property (tenant) using hotel name and city information if the tenant ID is not directly provided.

**Input Parameters:**
- `bookingId` (string, required): The guest's booking reference number
- `guestLastName` (string, required): The guest's last name for verification
- `tenantId` (string, optional): Direct tenant identifier if already known from a previous interaction. If not provided, `hotelName` and `city` must be provided instead.
- `hotelName` (string, conditionally required): Hotel name for tenant resolution. Required if `tenantId` is not provided.
- `city` (string, conditionally required): City location for tenant resolution. Required if `tenantId` is not provided.
- `country` (string, optional): Country for tenant resolution (helpful for international properties)

**Note:** The `tenantId` parameter is optional, but the tool requires either:
- A `tenantId` directly, OR
- Both `hotelName` and `city` to automatically resolve the tenant ID

In typical usage, guests will provide hotel name and city (not tenantId), and the system will automatically resolve the correct tenant identifier from the conversation context.

**Expected Output/Response:**
```json
{
  "content": [
    {
      "type": "text",
      "text": "Your check-in link has been generated. Please use this link to complete your check-in: https://ci.next.speeron.com/{tenantId}/checkin/{bookingId}?token={token}"
    }
  ],
  "isError": false
}
```

**Example Usage:**
```json
{
  "method": "tools/call",
  "params": {
    "name": "check_in_user",
    "arguments": {
      "bookingId": "ABC123",
      "guestLastName": "Smith",
      "hotelName": "Speeron Plaza Hotel",
      "city": "New York"
    }
  }
}
```

**Safety Annotations:**
- `readOnlyHint`: true (retrieves information and generates a link without modifying booking state)
- `destructiveHint`: false (non-destructive operation)

### 1.2 search_room_options (Search Available Hotel Rooms)

**What it does:** Searches for available room options at a specific hotel for the specified dates and guest configuration. Returns multiple options with different prices, amenities, and occupancy limits.

**Input Parameters:**
- `hotelName` (string, required): The name of the hotel
- `city` (string, required): The city where the hotel is located
- `numberOfNights` (integer, required): The number of nights for the stay
- `checkInDate` (datetime, required): The check-in date (ISO 8601 format: YYYY-MM-DD)
- `numberOfAdults` (integer, required): The number of adult guests
- `numberOfChildren` (integer, optional): The number of children guests (default: 0)
- `country` (string, optional): The country where the hotel is located
- `tenantId` (string, optional): The tenant ID if already known

**Expected Output/Response:**
```json
{
  "content": [
    {
      "type": "text",
      "text": "I found 3 available room option(s) at Speeron Plaza Hotel:\n\n**Option 1** (ID: opt-1)\nRoom Type: Standard Room\nDescription: Comfortable standard room with city view...\nPrice per night: $120.00\nTotal for 3 night(s): $360.00\n..."
    }
  ],
  "isError": false,
  "metadata": {
    "options": "[{\"OptionId\":\"opt-1\",\"RoomType\":\"Standard Room\",...}]",
    "checkInDate": "2024-03-15",
    "checkOutDate": "2024-03-18",
    "numberOfNights": 3,
    "numberOfAdults": 2,
    "numberOfChildren": 1,
    "hotelName": "Speeron Plaza Hotel"
  }
}
```

**Example Usage:**
```json
{
  "method": "tools/call",
  "params": {
    "name": "search_room_options",
    "arguments": {
      "hotelName": "Speeron Plaza Hotel",
      "city": "New York",
      "numberOfNights": 3,
      "checkInDate": "2024-03-15",
      "numberOfAdults": 2,
      "numberOfChildren": 1
    }
  }
}
```

**Safety Annotations:**
- `readOnlyHint`: true (searches and retrieves information without modifying data)
- `destructiveHint`: false (non-destructive operation)

### 1.3 search_rooms_by_area (Search Rooms by City/Area)

**What it does:** Searches for available room options in a specific city/area for the specified dates and guest configuration. Returns multiple options from Speeron Plaza Hotel with different prices.

**Input Parameters:**
- `city` (string, required): The city or area where to search for rooms
- `numberOfNights` (integer, required): The number of nights for the stay
- `checkInDate` (datetime, required): The check-in date (ISO 8601 format: YYYY-MM-DD)
- `numberOfAdults` (integer, required): The number of adult guests
- `numberOfChildren` (integer, optional): The number of children guests (default: 0)
- `country` (string, optional): The country where to search

**Expected Output/Response:**
```json
{
  "content": [
    {
      "type": "text",
      "text": "I found 3 available room option(s) at Speeron Plaza Hotel in New York:\n\n**Option 1** (ID: opt-1)\nRoom Type: Standard Room\n..."
    }
  ],
  "isError": false,
  "metadata": {
    "options": "[{\"OptionId\":\"opt-1\",...}]",
    "checkInDate": "2024-05-10",
    "checkOutDate": "2024-05-12",
    "numberOfNights": 2,
    "numberOfAdults": 2,
    "numberOfChildren": 0,
    "hotelName": "Speeron Plaza Hotel",
    "city": "New York"
  }
}
```

**Example Usage:**
```json
{
  "method": "tools/call",
  "params": {
    "name": "search_rooms_by_area",
    "arguments": {
      "city": "New York",
      "numberOfNights": 2,
      "checkInDate": "2024-05-10",
      "numberOfAdults": 2
    }
  }
}
```

**Safety Annotations:**
- `readOnlyHint`: true (searches and retrieves information without modifying data)
- `destructiveHint`: false (non-destructive operation)

### 1.4 confirm_booking (Confirm Room Booking)

**What it does:** Confirms a room booking by selecting one of the available options. Returns a booking confirmation with a booking number.

**Input Parameters:**
- `optionId` (string, required): The ID of the selected room option (must be one of: "opt-1", "opt-2", or "opt-3")
- `guestName` (string, required): The name of the guest making the booking
- `checkInDate` (datetime, required): The check-in date (ISO 8601 format: YYYY-MM-DD)
- `numberOfNights` (integer, required): The number of nights for the stay
- `numberOfAdults` (integer, required): The number of adult guests
- `numberOfChildren` (integer, optional): The number of children guests (default: 0)
- `hotelName` (string, optional): The name of the hotel (defaults to "Speeron Plaza Hotel")

**Expected Output/Response:**
```json
{
  "content": [
    {
      "type": "text",
      "text": "✅ **Booking Confirmed!**\n\nYour room has been successfully booked at Speeron Plaza Hotel.\n\n**Booking Details:**\n• Booking Number: **45**\n• Guest Name: Sarah Johnson\n• Room Type: Deluxe Room\n• Check-in Date: 2024-03-15\n• Check-out Date: 2024-03-18\n• Number of Nights: 3\n• Guests: 2 adult(s), 1 child(ren)\n• Total Price: $540.00\n\nWe look forward to welcoming you to Speeron Plaza Hotel!"
    }
  ],
  "isError": false
}
```

**Example Usage:**
```json
{
  "method": "tools/call",
  "params": {
    "name": "confirm_booking",
    "arguments": {
      "optionId": "opt-2",
      "guestName": "Sarah Johnson",
      "checkInDate": "2024-03-15",
      "numberOfNights": 3,
      "numberOfAdults": 2,
      "numberOfChildren": 1,
      "hotelName": "Speeron Plaza Hotel"
    }
  }
}
```

**Safety Annotations:**
- `readOnlyHint`: false (creates a booking, modifying system state)
- `destructiveHint`: false (creates new data, does not delete or modify existing bookings)

## 2. Authentication

### Authentication Method

The Speeron NEXT MCP server uses **anonymous access** - no authentication is required to connect to the server. The endpoints are publicly accessible and do not require API keys, OAuth tokens, or any other authentication credentials.

### Security Considerations

While authentication is not required, the server implements multiple security layers:

- **Rate Limiting**: Per-session and per-tenant rate limits prevent abuse
- **Exponential Backoff**: Automatic blocking after repeated failures
- **CORS Protection**: Requests are restricted to configured origins (ChatGPT domains)
- **Input Validation**: All parameters are validated before processing
- **Step-Up Authentication**: Additional verification required after suspicious activity patterns

### Required Scopes/Permissions

No scopes or permissions are required as the server operates with anonymous access.

### Token Refresh Procedures

Not applicable - the server does not use token-based authentication.

## Available Resources

None. The server provides action-oriented tools only.

## Available Prompts

None. The server provides tools for direct interaction with hotel systems.

## 3. Setup Instructions

### Prerequisites

- Access to Claude or another MCP-compatible AI assistant
- Internet connection to reach the MCP server endpoint
- Valid booking information (for check-in operations) or hotel details (for booking operations)

### How to Connect to the Server

The Speeron NEXT MCP server is a remote HTTP endpoint that can be connected via the MCP protocol.

#### Connection Configuration

Add the following configuration to your MCP client (e.g., Claude Desktop):

```json
{
  "mcpServers": {
    "speeron-next": {
      "url": "https://speeronnext-prod-chat-app.azurewebsites.net/mcp",
      "transport": "streamable-http"
    }
  }
}
```

#### Using server.json Configuration

Alternatively, use the server configuration file:

```json
{
  "$schema": "https://static.modelcontextprotocol.io/schemas/2025-10-17/server.schema.json",
  "name": "io.github.Speeron-Polska/speeron-next",
  "description": "Speeron NEXT Digital Guest Journey MCP server (remote HTTP endpoint).",
  "version": "1.0.0",
  "remotes": [
    {
      "type": "streamable-http",
      "url": "https://speeronnext-prod-chat-app.azurewebsites.net/mcp"
    }
  ]
}
```

### Configuration Options

The server supports the following configuration options (server-side):

- **Enabled/Disabled**: Feature flag to enable or disable the MCP server
- **Allowed Origins**: CORS configuration for browser-based clients
- **Rate Limits**: Configurable per-session and per-tenant limits
- **Exponential Backoff**: Configurable failure thresholds and block durations
- **Cool-Off Schedules**: Progressive cool-off period configuration

### Verification

After connecting, you can verify the connection by:
1. Checking that the server appears in your MCP client's list of available servers
2. Attempting to use one of the tools (e.g., `check_in_user`) with test parameters
3. Verifying that responses are received correctly

## 4. API Reference

### Endpoint URLs

**Base URL:** `https://speeronnext-prod-chat-app.azurewebsites.net/mcp`

**Discovery Endpoint:** `GET /` (returns server capabilities and tool definitions)

**MCP Endpoint:** `POST /mcp` (handles JSON-RPC requests and Server-Sent Events)

### Supported Transport Types

The server supports **Streamable HTTP** transport, which includes:

1. **JSON-RPC 2.0**: For synchronous request/response interactions
   - Method: POST
   - Content-Type: application/json
   - Request format: JSON-RPC 2.0 specification

2. **Server-Sent Events (SSE)**: For streaming responses and real-time updates
   - Method: GET
   - Content-Type: text/event-stream
   - Enables streaming tool responses and progress updates

### Request Format

#### JSON-RPC Request

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/call",
  "params": {
    "name": "check_in_user",
    "arguments": {
      "bookingId": "ABC123",
      "guestLastName": "Smith",
      "hotelName": "Speeron Plaza Hotel",
      "city": "New York"
    }
  }
}
```

#### SSE Connection

Connect to the SSE endpoint to receive streaming updates:

```
GET /mcp?sse=true
Accept: text/event-stream
```

### Response Format

#### Success Response

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "content": [
      {
        "type": "text",
        "text": "Your check-in link has been generated. Please use this link to complete your check-in: https://ci.next.speeron.com/{tenantId}/checkin/{bookingId}?token={token}"
      }
    ],
    "isError": false
  }
}
```

**Note:** The response includes the actual check-in URL in the text content. The URL is a fully functional link that guests can use to access their digital check-in process.

#### Error Response

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "error": {
    "code": -32602,
    "message": "Invalid params",
    "data": {
      "details": "Booking ID is required"
    }
  }
}
```

### HTTP Status Codes

- `200 OK`: Successful request
- `400 Bad Request`: Invalid request format or parameters
- `429 Too Many Requests`: Rate limit exceeded (includes `Retry-After` header)
- `500 Internal Server Error`: Server-side error
- `503 Service Unavailable`: Service temporarily unavailable (e.g., during maintenance)

## Use Cases with Example Prompts

### Use Case 1: Pre-Arrival Check-In Link Retrieval

**Scenario:** A guest has a booking but lost their check-in email or needs to retrieve their check-in link before arriving at the hotel.

**Example Prompts:**
- "I need to check in to my hotel. My booking ID is ABC123 and my last name is Smith. The hotel is Speeron Plaza Hotel in New York."
- "Can you help me get my check-in link? Booking number 789XYZ, last name Johnson, at Speeron Plaza Hotel in Chicago."
- "I'm checking in tomorrow. Booking ID: 456DEF, surname: Williams, hotel: Speeron Plaza Hotel, city: Los Angeles."

**Value:** Guests can quickly retrieve their personalized check-in links through natural conversation without navigating email or hotel websites, enabling seamless pre-arrival preparation.

### Use Case 2: Room Booking Search and Selection

**Scenario:** A guest wants to find and book a room at a hotel for specific dates, comparing different room types and prices.

**Example Prompts:**
- "I need a room at Speeron Plaza Hotel in New York for 3 nights starting March 15th. Two adults and one child."
- "Show me available rooms in Chicago for 2 nights from April 1st. I need a room for 2 adults."
- "What rooms are available at Speeron Plaza Hotel in Los Angeles? Check-in March 20th, 4 nights, 2 adults."

**Value:** Guests can search for rooms, compare options (Standard, Deluxe, Suite) with pricing and amenities, and make informed booking decisions through conversational interaction.

### Use Case 3: Area-Based Room Discovery

**Scenario:** A guest is planning a trip but hasn't decided on a specific hotel yet. They want to explore available options in a city.

**Example Prompts:**
- "I'm looking for a hotel room in New York for 2 nights, check-in on May 10th, for 2 adults. What's available?"
- "Find me a room in Chicago for 3 nights starting June 1st. One adult."
- "What hotel rooms are available in Los Angeles? 5 nights from July 15th, 2 adults and 2 children."

**Value:** Guests can discover and book hotel rooms by city without needing to know specific hotel names, making trip planning more flexible and convenient.

### Use Case 4: Booking Confirmation After Search

**Scenario:** A guest has reviewed room options and wants to confirm their booking selection.

**Example Prompts:**
- "I'll take option 2, the Deluxe Room. Guest name: Sarah Johnson, check-in March 15th, 3 nights, 2 adults."
- "Book option 1 for me. Name: Michael Brown, dates: April 1-3, 2 nights, 2 adults."
- "Confirm booking for option 3, the Suite. Guest: David Lee, check-in May 10th, 4 nights, 2 adults and 1 child."

**Value:** After reviewing room options, guests can complete their booking in the same conversation, receiving immediate confirmation with booking details and confirmation number.

## Technical Architecture

### Integration Architecture

The MCP server is co-hosted as a module within the existing Speeron chatbot application, providing:
- **Unified Infrastructure**: Shared logging, dependency injection, configuration, and deployment pipeline
- **Efficient Resource Usage**: Single process eliminates overhead of separate application hosting
- **Modular Design**: Clear separation of concerns with dedicated MCP module structure
- **Future Extensibility**: Easy addition of new tools and features within the same bounded context

### Tenant Resolution

The server includes intelligent tenant directory resolution that:
- Maps hotel names and cities to tenant identifiers
- Supports optional country specification for international properties
- Caches tenant lookups for improved performance
- Provides clear error messages when hotels cannot be found

### Booking Search Integration

The check-in tool integrates with Speeron's booking search system to:
- Query bookings by tenant ID, booking ID, and guest last name
- Return secure, personalized check-in URLs
- Handle booking not found scenarios gracefully
- Support both simple and full booking ID formats

## Security and Compliance

The server implements multiple layers of security to protect against abuse and ensure reliable service:

- Rate Limiting
- Exponential Backoff
- Cool-Off Mechanism
- Step-Up Authentication
- CORS Configuration

## 5. Error Handling

### Common Error Codes

#### JSON-RPC Error Codes

- **-32600 (Invalid Request)**: The JSON sent is not a valid Request object
- **-32601 (Method Not Found)**: The method does not exist or is not available
- **-32602 (Invalid Params)**: Invalid method parameter(s)
- **-32603 (Internal Error)**: Internal JSON-RPC error
- **-32700 (Parse Error)**: Invalid JSON was received

#### Application Error Codes

- **BOOKING_NOT_FOUND**: The specified booking could not be found
- **TENANT_RESOLUTION_FAILED**: Could not resolve hotel property from provided information
- **INVALID_PARAMETER**: Required parameter is missing or invalid
- **RATE_LIMIT_EXCEEDED**: Request rate limit has been exceeded
- **SESSION_ERROR**: Session-related error occurred

### Error Response Format

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "error": {
    "code": -32602,
    "message": "Invalid params",
    "data": {
      "errorCode": "INVALID_PARAMETER",
      "details": "Booking ID is required. Please provide your booking ID.",
      "isError": true
    }
  }
}
```

### Common Error Scenarios

#### 1. Missing Required Parameters

**Error:** `INVALID_PARAMETER`  
**Message:** "Booking ID is required. Please provide your booking ID."  
**Solution:** Ensure all required parameters are provided in the tool call.

#### 2. Booking Not Found

**Error:** `BOOKING_NOT_FOUND`  
**Message:** "Could not find your booking. Please verify your booking ID and last name, and ensure you've provided the correct hotel name and city."  
**Solution:** Verify the booking ID, guest last name, and hotel information are correct.

#### 3. Hotel/Tenant Resolution Failed

**Error:** `TENANT_RESOLUTION_FAILED`  
**Message:** "Could not find a hotel named 'Hotel Name' in City. Please verify the hotel name and city."  
**Solution:** Provide accurate hotel name and city. Optionally include country for international properties.

#### 4. Rate Limit Exceeded

**Error:** `RATE_LIMIT_EXCEEDED`  
**HTTP Status:** `429 Too Many Requests`  
**Headers:** `Retry-After: 60` (seconds)  
**Message:** "Rate limit exceeded. Please try again later."  
**Solution:** Wait for the specified duration before retrying. The `Retry-After` header indicates when to retry.

#### 5. Session Issues

**Error:** `SESSION_ERROR`  
**Message:** "It looks like there's a session issue. Please disconnect and reconnect from the Speeron check-in connector to establish a fresh session."  
**Solution:** Disconnect and reconnect the MCP server connection.

### Troubleshooting Guidance

#### Issue: Tool calls are not working

**Checklist:**
1. Verify the server is enabled and accessible
2. Check that the endpoint URL is correct
3. Ensure all required parameters are provided with correct types
4. Verify network connectivity to the server

#### Issue: Getting rate limit errors frequently

**Solutions:**
1. Reduce the frequency of requests
2. Wait for the cool-off period to expire
3. Check if multiple sessions are active simultaneously
4. Contact support if legitimate use is being blocked

#### Issue: Booking searches return no results

**Solutions:**
1. Verify booking ID format matches the hotel's system
2. Ensure guest last name matches exactly (case-sensitive in some systems)
3. Confirm hotel name and city are correct
4. Try providing country information for international properties

#### Issue: Connection timeouts

**Solutions:**
1. Check network connectivity
2. Verify the server endpoint is accessible
3. Check if the server is under maintenance
4. Retry the request after a short delay

### Error Recovery

The server implements automatic error recovery mechanisms:

- **Exponential Backoff**: Automatic retry delays for transient failures
- **Negative Result Caching**: Prevents repeated failed queries for the same data
- **Session Recovery**: Automatic session re-establishment for connection issues
- **Graceful Degradation**: Provides helpful error messages instead of generic failures

## Configuration

The server can be configured via application settings with feature flags for:
- Enabling/disabling the MCP server
- Configuring rate limits and thresholds
- Setting up exponential backoff parameters
- Defining cool-off schedules
- Enabling step-up authentication requirements
- CORS origin restrictions

## Future Enhancements

The architecture supports future expansion including:
- Guest portal tools for in-stay services
- Additional hotel service integrations
- Enhanced booking search capabilities
- Multi-language support

## Publisher Information

**Publisher:** Speeron Polska  
**License:** Proprietary  
**Support:** Contact Speeron support for integration assistance

