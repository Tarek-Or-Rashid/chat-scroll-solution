# Chat Auto-Scroll Challenge

## Setup

1. Get a free Gemini API key from [ai.google.dev](https://ai.google.dev)
2. Run `flutter pub get`
3. Run `flutter run` (web, macOS, or any platform)
4. Enter your API key and start chatting

## UX Issues Identified and Fixed

### Issue 1: No Auto-Scroll During Streaming
**Problem:** When the AI streamed a response token by token, the chat list did not automatically scroll to the bottom. Users had to manually scroll down to see new content.

**Fix:** Added a `_scrollToBottom()` call inside the stream listener on every incoming token. When `_autoScroll` is `true`, the list animates to `maxScrollExtent` after each frame using `SchedulerBinding.addPostFrameCallback`.

---

### Issue 2: Manual Scroll Did Not Pause Auto-Scroll
**Problem:** When the user scrolled up manually while a response was streaming, the list would jump back to the bottom, making it impossible to read previous messages.

**Fix:** Added a `ScrollController` listener `_onScroll()` that detects when the user scrolls away from the bottom (more than 32px from `maxScrollExtent`) and sets `_autoScroll = false`, pausing the auto-scroll behavior.

---

### Issue 3: Sending a New Message While Scrolled Up Did Not Jump to Bottom
**Problem:** If the user was scrolled up and sent a new message, the new message and its streaming response were not visible — the list stayed at the old scroll position.

**Fix:** In `_handleMessageSend()`, `_autoScroll` is reset to `true` and `_scrollToBottom()` is called immediately after inserting the user message, so the view always jumps to the latest content on send.

---

### Issue 4: Scrolling Back to Bottom Did Not Resume Auto-Scroll
**Problem:** Once the user manually scrolled away, auto-scroll was permanently disabled for that streaming session, even if the user scrolled back to the bottom.

**Fix:** The `_onScroll()` listener also detects when the user returns to within 32px of the bottom and sets `_autoScroll = true`, resuming auto-scroll automatically.

---

## Deployed URL

🔗 [Live Demo](https://tarek-or-rashid.github.io/chat-scroll-solution/)

---

## Screen Recordings

All four scenarios are demonstrated in a single recording:

* [Watch Full Demo Recording](https://drive.google.com/drive/folders/1g7-HvXIET2rv_tvDSK6TjOfD5VpQIYfP)

The recording covers:
- Scenario 1 (Basic Auto-Scroll): AI response streams in and screen auto-scrolls to bottom
- Scenario 2 (Pause on Manual Scroll): Manual scroll up pauses auto-scroll
- Scenario 3 (Send While Scrolled Up): Sending a new message jumps back to bottom
- Scenario 4 (Resume Auto-Scroll After Scroll Down): Scrolling back to bottom resumes auto-scroll
