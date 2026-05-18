# 🔁 n8n — Loop Over Items Guide

> n8n এ Loop Over Items ব্যবহার করে Google Sheets এর প্রতিটি Row এ আলাদা আলাদা Email পাঠানোর গাইড।

🔗 **পূর্বশর্ত:**
- [n8n Self-Host Setup](https://github.com/Omarmdwasimuddin/n8n-Self-Host-Setup)
- [n8n OAuth Setup](https://github.com/Omarmdwasimuddin/n8n-OAuth-02)

---

## 📊 Workflow Overview

```
Google Sheets
(Get All Rows)
      │
      ▼
Loop Over Items ◄──────────────────┐
      │                            │
      ▼ (loop)                     │
  Gmail: Send Message              │
      │                            │
      ▼                            │
    Wait ───────────────────────────┘
      │
      ▼ (done)
   Summarize
      │
      ▼
Gmail: Send Summary (Admin)
```

---

## ⚙️ Workflow সেটআপ

### ✅ ধাপ ১ — Google Sheets Node যোগ করো

- নতুন **Workflow** তৈরি করো
- **Nodes Panel** ওপেন করো
- **"Google Sheets"** সার্চ করো এবং ক্লিক করো
- **Actions (10)** এ ক্লিক করো
- **"Get row(s) in sheet"** সিলেক্ট করো
- প্রয়োজনীয় **Value** সেট করো

![Google Sheets Get Rows](https://imgur.com/DlFtHqz.png)

---

### ✅ ধাপ ২ — Loop Over Items Node যোগ করো

- **Get row(s) in sheet** এর **`+`** বাটনে ক্লিক করো
- **"Loop Over Items"** সার্চ করো এবং ক্লিক করো
- **Batch Size** সেট করো — Google Sheets এর Data এর পরিমাণ অনুযায়ী

> 💡 **Batch Size কী?** একবারে কতটি Row Process হবে। যেমন Batch Size = 1 হলে একটি একটি করে Row Process হবে।

![Loop Over Items](https://imgur.com/XDUWQ9R.png)

---

### ✅ ধাপ ৩ — Replace Me Node মুছে ফেলো

Loop Over Items এর ভেতরে **"Replace Me"** নামের Default Node টি **Delete** করো।

![Delete Replace Me](https://imgur.com/OTWKgTG.png)

---

### ✅ ধাপ ৪ — Gmail Node যোগ করো (Loop এর ভেতরে)

- **Loop Over Items** এর **Loop** এর **`+`** বাটনে ক্লিক করো
- **"Gmail"** সার্চ করো এবং ক্লিক করো
- **"Send a message"** সিলেক্ট করো
- প্রতিটি Row এর জন্য Email এর **Value** সেট করো

![Gmail Send Message](https://imgur.com/GIMqNfV.png)

---

### ✅ ধাপ ৫ — Wait Node যোগ করো

- **Send a message** এর **`+`** বাটনে ক্লিক করো
- **"Wait"** সার্চ করো এবং ক্লিক করো
- **Value** সেট করো *(যেমন: প্রতিটি Email এর মাঝে কত সেকেন্ড অপেক্ষা করবে)*

> ⏳ **Wait কেন দরকার?** একসাথে অনেক Email পাঠালে Gmail Spam হিসেবে চিহ্নিত করতে পারে। Wait দিলে সেটা এড়ানো যায়।

![Wait Node](https://imgur.com/DBY5h5E.png)

---

### ✅ ধাপ ৬ — Wait Node কে Loop এর সাথে Connect করো

- **Wait Node** এর Output কে **Loop Over Items** Node এর সাথে **Connect** করো
- **Execute Workflow** বাটনে ক্লিক করো এবং Loop ঠিকমতো কাজ করছে কিনা যাচাই করো

![Connect Wait to Loop](https://imgur.com/v6e4V4u.png)

---

### ✅ ধাপ ৭ — Summarize Node যোগ করো

সব Row Process শেষ হলে একটি Summary তৈরি করো:

- **Loop Over Items** এর **Done** এর **`+`** বাটনে ক্লিক করো
- **"Summarize"** সার্চ করো এবং ক্লিক করো
- **Value** সেট করো *(কী Summary দেখাবে)*

![Summarize Node](https://imgur.com/knnVl63.png)

---

### ✅ ধাপ ৮ — Admin কে Summary Email পাঠাও

- **Summarize** এর **`+`** বাটনে ক্লিক করো
- **"Gmail"** সার্চ করো → **"Send a message"** সিলেক্ট করো
- **To** তে **Admin এর Email** দাও
- Value সেট করো

![Gmail Admin Summary](https://imgur.com/txcL0DV.png)

---

### ✅ ধাপ ৯ — Final Execution করো

**Execute Workflow** বাটনে ক্লিক করো।

> ✔️ Workflow সফলভাবে চললে:
> - Google Sheets এর প্রতিটি Row এ আলাদা Email যাবে
> - সব শেষে Admin এর কাছে একটি Summary Email যাবে

---

## 📚 Loop Over Items কী এবং কেন দরকার?

### সহজ ভাষায়:

> Google Sheets এ যদি **১০০টি Row** থাকে, তাহলে প্রতিটি Row এর জন্য আলাদা আলাদা Email পাঠাতে **Loop Over Items** ব্যবহার করতে হয়।

```
Row 1 → Email পাঠাও → Wait
Row 2 → Email পাঠাও → Wait
Row 3 → Email পাঠাও → Wait
  ...
Row 100 → Email পাঠাও → Wait
        │
        ▼
   সব শেষ → Summary Email (Admin)
```

### Loop এর দুটি Output:

| Output | কখন চলে | কাজ |
|--------|----------|-----|
| **Loop** | প্রতিটি Item এর জন্য | Gmail → Wait → আবার Loop |
| **Done** | সব Item শেষ হলে | Summarize → Admin Email |

---

## 📋 Quick Reference

| ধাপ | Node | কাজ |
|-----|------|-----|
| ১ | Google Sheets | সব Row পড়ো |
| ২ | Loop Over Items | একটি একটি করে Process করো |
| ৩ | — | Replace Me মুছে ফেলো |
| ৪ | Gmail | প্রতিটি Row এ Email পাঠাও |
| ৫ | Wait | প্রতিটি Email এর মাঝে অপেক্ষা করো |
| ৬ | — | Wait কে Loop এ Connect করো |
| ৭ | Summarize | সব শেষে Summary তৈরি করো |
| ৮ | Gmail | Admin কে Summary Email পাঠাও |
| ৯ | — | Execute করো |

---

*Loop Over Items ব্যবহার করলে একটি Workflow দিয়ে হাজার হাজার মানুষকে আলাদা আলাদা Email পাঠানো সম্ভব।*
