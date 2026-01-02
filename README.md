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
Deep integration with Property Management Systems (PMS) including Mews, enabling real-time synchronization of bookings, guest data, and room availability. The platform supports various PMS adapters for flexible hotel system connectivity.

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

## Server Overview

**Server Name:** `io.github.Speeron-Polska/speeron-next`  
**Version:** 1.0.0  
**Protocol:** Model Context Protocol (MCP)  
**Transport:** Streamable HTTP (JSON-RPC and Server-Sent Events)  
**Framework:** .NET 9.0 with ASP.NET Core

## Brief Description (Subtitle)

Hotel check-in and room booking

## Extended Description (50-100 words)

The Speeron NEXT MCP server provides hotel guest services through natural language interactions. It offers automated check-in link generation by searching bookings with booking ID and guest last name, supporting multi-tenant hotel properties with intelligent tenant resolution. The server also provides room availability search with multiple pricing options and booking confirmation capabilities, seamlessly integrating hotel operations into conversational experiences.

## Available Tools

### 1. check_in_user (Hotel Check-In Link Retrieval)

Helps guests retrieve their personalized check-in link by searching for their booking using booking ID and guest last name. The tool automatically resolves the hotel property using hotel name and city information, or uses a tenant ID if already known.

**Parameters:**
- `bookingId` (required): The guest's booking reference number
- `guestLastName` (required): The guest's last name for verification
- `hotelName` (optional): Hotel name for tenant resolution
- `city` (optional): City location for tenant resolution
- `country` (optional): Country for tenant resolution
- `tenantId` (optional): Direct tenant identifier if known

**Returns:** A personalized check-in URL that guests can use to complete their digital check-in process.

**Safety Annotations:**
- `readOnlyHint`: true (retrieves information and generates a link without modifying booking state)
- `destructiveHint`: false (non-destructive operation)

### 2. search_room_options (Search Available Hotel Rooms)

Searches for available room options at a specific hotel for the specified dates and guest configuration. Returns multiple options with different prices, amenities, and occupancy limits.

**Parameters:**
- `hotelName` (required): The name of the hotel
- `city` (required): The city where the hotel is located
- `numberOfNights` (required): The number of nights for the stay
- `checkInDate` (required): The check-in date
- `numberOfAdults` (required): The number of adult guests
- `numberOfChildren` (optional): The number of children guests (default: 0)
- `country` (optional): The country where the hotel is located
- `tenantId` (optional): The tenant ID if already known

**Returns:** A list of available room options (Standard, Deluxe, Suite) with pricing, descriptions, amenities, and occupancy details.

**Safety Annotations:**
- `readOnlyHint`: true (searches and retrieves information without modifying data)
- `destructiveHint`: false (non-destructive operation)

### 3. search_rooms_by_area (Search Rooms by City/Area)

Searches for available room options in a specific city/area for the specified dates and guest configuration. Returns multiple options from Speeron Plaza Hotel with different prices.

**Parameters:**
- `city` (required): The city or area where to search for rooms
- `numberOfNights` (required): The number of nights for the stay
- `checkInDate` (required): The check-in date
- `numberOfAdults` (required): The number of adult guests
- `numberOfChildren` (optional): The number of children guests (default: 0)
- `country` (optional): The country where to search

**Returns:** A list of available room options from Speeron Plaza Hotel in the specified area with pricing and details.

**Safety Annotations:**
- `readOnlyHint`: true (searches and retrieves information without modifying data)
- `destructiveHint`: false (non-destructive operation)

### 4. confirm_booking (Confirm Room Booking)

Confirms a room booking by selecting one of the available options. Returns a booking confirmation with a booking number.

**Parameters:**
- `optionId` (required): The ID of the selected room option (opt-1, opt-2, or opt-3)
- `guestName` (required): The name of the guest making the booking
- `checkInDate` (required): The check-in date
- `numberOfNights` (required): The number of nights for the stay
- `numberOfAdults` (required): The number of adult guests
- `numberOfChildren` (optional): The number of children guests (default: 0)
- `hotelName` (optional): The name of the hotel (defaults to Speeron Plaza Hotel)

**Returns:** A booking confirmation with booking number, room details, dates, guest information, and total price.

**Safety Annotations:**
- `readOnlyHint`: false (creates a booking, modifying system state)
- `destructiveHint`: false (creates new data, does not delete or modify existing bookings)

## Available Resources

None. The server provides action-oriented tools only.

## Available Prompts

None. The server provides tools for direct interaction with hotel systems.

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

### Rate Limiting
- **Per-Session Limits**: 5 requests per minute with burst allowance of 3
- **Per-Tenant Limits**: 100 requests per minute across all sessions
- **Distinct Booking ID Limits**: 3 per session per minute, 10 per session per hour
- **Negative Result Caching**: 90-second TTL for failed booking searches to prevent abuse

### Exponential Backoff
- Multi-level failure tracking with automatic blocking
- Tenant-level blocking after 8+ failures (15-minute block)
- Specific-level blocking for tenant+lastName combinations (60-minute block)
- Global tenant failure threshold protection (300 failures in 10 seconds triggers 60-minute block)
- Per-tenant rate limit failure threshold (80 failures in 30 minutes triggers 60-minute block)

### Cool-Off Mechanism
Progressive cool-off periods (10s, 30s, 2m, 10m) applied at multiple levels:
- Session ID
- Tenant ID
- Tenant + Last Name
- Tenant + Booking ID Prefix

### Step-Up Authentication
- Automatically triggers after 3 distinct booking ID attempts in a session
- Requires additional discriminator (arrival date) for enhanced security

### CORS Configuration
- Configured for browser-based authentication flows
- Environment-specific origin restrictions
- Support for credentials in cross-origin requests

## Error Handling

The server provides user-friendly error messages for:
- Missing or invalid parameters
- Booking not found scenarios
- Hotel/tenant resolution failures
- Session-related issues
- Rate limiting and security blocks

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

