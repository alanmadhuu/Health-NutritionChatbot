# 🥗 Health-NutritionChatbot - Your AI Advisor for Stronger Communities 🛡️

This n8n workflow automates the process of providing accessible, general health and nutrition advice, along with localized recipe suggestions, through a conversational AI. It's designed to support individuals, particularly in rural areas, by bridging information gaps.

**Crucial Disclaimer:** This chatbot is an AI assistant and **NOT a medical professional**. The advice provided is general, non-diagnostic, and should **NEVER** replace consultation with a qualified doctor or healthcare provider. For any health concerns or emergencies, users are strongly advised to seek immediate professional medical attention.

---

## 🚀 Features

-   **Conversational AI (Google Gemini):** Understands user queries related to health, weakness, and nutrition, and generates personalized, non-diagnostic advice and recipes.
-   **Multi-Channel Communication:**
    -   **Telegram Integration:** Receives user messages via a Telegram bot and sends AI responses directly back.
-   **Automated Logging (Google Sheets):** All interactions (user query, AI response, sender ID, timestamp) are automatically logged for monitoring, review, and analytics.
-   **Emergency Flagging:** Critically, if the AI's response suggests a serious medical concern (e.g., "seek immediate medical attention," "dial 108"), the interaction is automatically flagged in the logs for potential human follow-up.
-   **Localized Recipe Suggestions:** AI is prompted to suggest recipes using common Indian ingredients, focusing on seasonal and regional applicability.

---

## 📦 Workflow Overview

1.  **Telegram Trigger (or Twilio Trigger):** Watches for new incoming messages (from users) via the configured communication channel.
2.  **Prompt for Gemini (Function Node):** Extracts the user's message and sender ID from the raw incoming data. It then crafts a detailed, constrained prompt for the Google Gemini AI, instructing it to act as a non-diagnostic health advisor for rural India.
3.  **HTTP Request (Google Gemini API):** Sends the prepared prompt to the Google Gemini API (e.g., `gemini-1.5-pro-latest` or `gemini-1.5-flash-latest`). It receives the AI's generated health advice/recipe.
4.  **Process Gemini Output (Function Node):** Extracts the clean, readable text response from Gemini's complex JSON output. It also carries forward the user's original query and sender ID, and checks the AI's response for emergency keywords to set a `FlagForReview`.
5.  **Prepare Data for Google Sheets (Set Node):** Organizes and explicitly names the data fields (`Timestamp`, `UserQuery`, `AIResponse`, `FlagForReview`, `SenderTelegramID`/`SenderPhoneNumber`) to precisely match the column headers in the logging Google Sheet.
6.  **Append row in sheet (Google Sheets Node):** Appends a new row to a designated Google Sheet, logging the complete interaction details.
7.  **Send a text message (Telegram Send Message or Twilio Send SMS):** Sends the AI's final, clean response back to the user via their originating communication channel.

---

## 📂 Required Credentials

To use this workflow, set up the following credentials in your n8n instance:

-   ✅ **Telegram Bot Token** — to connect to your Telegram bot.
-   ✅ **Google Gemini API** — for generative AI capabilities.
-   ✅ **Google Sheets OAuth2** — to log interactions in a Google Sheet.

> 🛡️ Credentials are referenced by ID in the workflow. You’ll need to reassign them after importing this workflow.

---

## 🧪 Example Output (from Gemini via "Process Gemini Output" Node)

This is a sample of the structured data output from the `Process Gemini Output` node, representing the AI's response. The `finalResponseToSend` is what the user would receive via message.

```json
[
  {
    "userQuery": "Hi, my bones are feeling weak, please help me suggest a good nutrition plan.",
    "sender": "telegram_chat_id:5608639526",
    "finalResponseToSend": "I am an AI assistant and not a medical professional. For any health concerns, please consult a qualified doctor.\n\nHello Alan! Feeling weak can have many causes, so it's important to consult a doctor to rule out any underlying medical conditions. However, a healthy diet can significantly boost your energy levels and immunity. I can suggest some dietary changes that might help, but remember this is not a replacement for professional medical advice.\n\nTo feel stronger and improve your immunity, focus on a diet rich in:\n\n* **Fruits and Vegetables:** These are packed with vitamins and minerals. Try to include a variety of colorful options – the more colors, the better the nutritional diversity! Seasonal options are best and usually the most affordable. For example, if it's mango season, include mangoes in your diet; during winter months, focus on root vegetables like carrots and sweet potatoes.\n\n* **Legumes (Dal):** Lentils and beans are excellent sources of protein and fiber, crucial for energy and a healthy gut. A simple dal with turmeric and ginger is both nutritious and easy to make.\n\n* **Whole Grains:** Choose whole grains like brown rice, jowar (sorghum), bajra (pearl millet), or ragi over refined grains. These provide sustained energy and fiber.\n\n* **Nuts and Seeds:** A small handful of almonds, peanuts, or sunflower seeds can provide healthy fats and essential nutrients.\n\n\nHere's a simple recipe idea using readily available ingredients, focusing on seasonal vegetables (adjust based on your region and season):\n\n\n**Spinach and Moong Dal Khichdi:**\n\n**Ingredients:**\n\n* 1 cup moong dal (split yellow lentils), rinsed\n* 1 cup water\n* 1 cup chopped spinach\n* 1/2 cup chopped onion\n* 1 tsp ginger, grated\n* 1/2 tsp turmeric powder\n* 1/4 tsp red chili powder (optional)\n* Salt to taste\n* 1 tbsp ghee or oil\n\n**Instructions:**\n\n1. In a pot, combine moong dal and water. Bring to a boil, then reduce heat and simmer for 15-20 minutes, or until the dal is cooked and soft.\n2. Add the onion and ginger and sauté for a minute until fragrant.\n3. Add the turmeric and chili powder. Stir well.\n4. Add the chopped spinach and salt, and cook for another 2-3 minutes until the spinach wilts.\n5. Add ghee or oil for extra flavor (optional).\n\nThis khichdi is a complete meal, providing protein, carbohydrates, and essential nutrients. You can adjust the spices and vegetables to your preference. Remember to drink plenty of water throughout the day as well.\n\n\nAgain, if your weakness persists or you have other health concerns, please consult a doctor for proper diagnosis and treatment.\n",
    "flagForReview": false,
    "timestamp": "2025-07-21T14:43:44.026Z"
  }
]
