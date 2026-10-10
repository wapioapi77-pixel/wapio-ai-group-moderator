# 🛡️ Autonomous AI WhatsApp Group Moderator (n8n + Wapio)

> An autonomous, real-time WhatsApp community moderator built with **n8n** and **Wapio** (`n8n-nodes-wapio`).

Whenever an unauthorized link, scam, or abusive message is posted in a managed WhatsApp group:
1. **Public Group Warning:** The bot immediately posts a quoted reply in the group, tagging the offender via `@mention` with a polite rule reminder.
2. **Private Admin Alert:** The bot instantly sends a formatted WhatsApp DM to the group's Admin containing the offender's phone number, original message, and exact WhatsApp Message ID.
3. **Zero Spam / Zero False Positives:** Normal conversation, casual banter, and capitalization are completely ignored.

---

## ⚡ Workflow Architecture (9 Nodes)

```
[1. Wapio Trigger] ➔ [2. Filter Outgoing (fromMe == false)] ➔ [3. Group and Admin Config] ➔ [4. Jev AI Decision Engine]
                                                                                                    │
                                                  ┌─────────────────────────────────────────────────┴─────────────────────────────────────────────────┐
                                                  ▼ (Branch 0: Link Violation)                                                        ▼ (Branch 1: Abusive Language)
                              [5. 💬 Quoted Reply: Link Rule]                                                     [7. 💬 Quoted Reply: Respect Rule]
                                                  │                                                                                   │
                                                  ▼                                                                                   ▼
                              [6. 🚨 Alert Admin: Link Shared]                                                    [8. 🚨 Alert Admin: Abusive Language]
```

---

## 🚀 1-Minute Quickstart

### 1. Prerequisites
* **n8n** installed and running (self-hosted or cloud).
* **Wapio Community Node:** Install `n8n-nodes-wapio` in n8n (**Settings** ➔ **Community Nodes** ➔ Install `n8n-nodes-wapio`).
* A connected WhatsApp session on [Wapio](https://wapio.dev).

### 2. Import the Workflow
1. Open n8n and click **Workflow Menu (top right)** ➔ **Import from File...**
2. Select [`wapio_group_moderator.json`](./wapio_group_moderator.json).
3. Connect your **Wapio API Credential** and select your connected WhatsApp session on the nodes.

### 3. Configure Your Groups & Admins
Open node **`Group and Admin Config`** and update the `MANAGED_GROUPS` mapping:

```javascript
const MANAGED_GROUPS = {
  '120363412067748618@g.us': '212673684266', // 'GROUP_JID@g.us': 'ADMIN_PHONE_NUMBER'
  // You can add more groups:
  // '120363999999999999@g.us': '14155552671'
};
```

> **How to get your Group JID?**  
> Send any message in the group while your Wapio Trigger is listening, or view your group list in the Wapio dashboard. Group JIDs always end with `@g.us`.

### 4. Activate
Toggle the workflow to **Active** (or click **Test workflow** to test live).

---

## 📋 Features

* **Multi-Group Architecture:** Moderate dozens of different groups from a single workflow, each routing alerts to its own designated admin.
* **Quoted Replies with @Mentions:** WhatsApp highlights the offender in blue clickable text (`@phone`), delivering a direct push notification to the user.
* **Instant DM Incident Report:** Admins receive the offender's phone number, full message text, and WhatsApp Message ID directly in WhatsApp DM so they can take immediate action if needed.
* **Loop Prevention:** The `fromMe == false` filter guarantees the bot never responds to its own messages.
* **Zero Latency:** Rule evaluation runs in <1 millisecond without external API lag.

---

## 🛠️ Customizing Moderation Rules

In node **`Jev AI Decision Engine (<300ms)`**, you can customize:

1. **Link Detection:**
   ```javascript
   const urlRegex = /(https?:\/\/[^\s]+|www\.[^\s]+|bit\.ly\/[^\s]+|t\.me\/[^\s]+|wa\.me\/[^\s]+|chat\.whatsapp\.com\/[^\s]+|discord\.gg\/[^\s]+)/i;
   ```
2. **High-Risk Scam Keywords:**
   ```javascript
   const scamKeywords = ['airdrop', 'free crypto', 'pump signal', 'forex signals', 'guaranteed profit'];
   ```
3. **Abusive Language Filter:**
   ```javascript
   const hateWords = ['fuck', 'bitch', 'asshole', 'stfu', 'dick'];
   ```

---

## 📁 Repository Structure

```
├── wapio_group_moderator.json   # Ready-to-import n8n workflow
├── README.md                    # Setup and configuration guide
└── voiceover-audio/             # Tutorial voiceover narration audio files
```

---

## 📄 License
MIT License. Free to use, modify, and integrate into your community workflows.
