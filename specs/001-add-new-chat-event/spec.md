# Feature Specification: New Chat Event

**Feature Branch**: `001-add-new-chat-event`  
**Created**: 2026-03-13  
**Status**: Draft  
**Input**: User description: "Crea un nuevo evento new_chat, que se mande cada vez que se crea un nuevo chat"

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Receive notification when a new chat is created (Priority: P1)

As a user who integrates with the WhatsApp API, I want to receive a notification event whenever a new chat conversation is created in the system, so that I can trigger automated workflows or track new conversations in real-time.

**Why this priority**: This is the core functionality requested - without this event, users cannot build automated responses or track new conversations programmatically.

**Independent Test**: Can be tested by sending a message to a contact that has no existing chat history, and verifying the new_chat event is received via webhook.

**Acceptance Scenarios**:

1. **Given** a user has subscribed to the "new_chat" event, **When** a new chat is created (first message sent or received), **Then** the system sends a webhook notification with event type "new_chat" and chat details.

2. **Given** a user has NOT subscribed to the "new_chat" event, **When** a new chat is created, **Then** no webhook notification is sent for this event type.

3. **Given** a user has subscribed to "All" events, **When** a new chat is created, **Then** the system sends a webhook notification including the "new_chat" event.

---

### User Story 2 - Access chat details in the event (Priority: P1)

As a user receiving the new_chat event, I want to receive sufficient information about the new chat in the event payload, so that I can identify and process the conversation without making additional API calls.

**Why this priority**: The event is only useful if it contains enough information to identify the chat and take action.

**Independent Test**: Can be tested by verifying the event payload contains the chat JID, timestamp, and participant information.

**Acceptance Scenarios**:

1. **Given** a new chat is created, **When** the new_chat event is sent, **Then** the payload includes the chat's unique identifier (JID), creation timestamp, and participant information.

---

### Edge Cases

- What happens when the chat is a group vs individual chat?
- What happens if the webhook fails to send - should the event be retried or logged?
- What happens when a chat already exists - should the event be sent again?
- How does the system handle very high volumes of new chats being created simultaneously?

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST add "new_chat" to the list of supported event types.
- **FR-002**: System MUST trigger the new_chat event when the first message is sent or received in a conversation that has no prior messages in the system.
- **FR-003**: System MUST include chat identification information (chat JID) in the event payload.
- **FR-004**: System MUST include a timestamp indicating when the chat was first active in the event payload.
- **FR-005**: System MUST respect user event subscriptions - only send new_chat events to users who have subscribed to "new_chat" or "All".
- **FR-006**: System MUST NOT send duplicate new_chat events for the same chat once it has been created.
- **FR-007**: System MUST differentiate between individual chats and group chats in the event payload.

### Key Entities *(include if feature involves data)*

- **Chat**: A conversation between two or more participants. Key attributes include unique identifier (JID), creation timestamp, and participant list.
- **Event Subscription**: User preference that determines which event types are delivered via webhook.
- **Event Payload**: Structured data sent via webhook containing event type and relevant information.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Users who subscribe to "new_chat" event receive a webhook notification within 5 seconds of a new chat being created.
- **SC-002**: The new_chat event payload contains all required fields (chat JID, timestamp, participants) in 100% of successful deliveries.
- **SC-003**: Users can subscribe to "new_chat" event using the same subscription mechanism as other event types.
- **SC-004**: No duplicate new_chat events are sent for the same chat conversation.
- **SC-005**: System maintains backward compatibility - existing event subscriptions continue to work without modification.
