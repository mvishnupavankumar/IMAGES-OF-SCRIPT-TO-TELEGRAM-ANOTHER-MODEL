# IMAGES-OF-SCRIPT-TO-TELEGRAM-ANOTHER-MODEL
🌌 AI Anime Visual Story Generator to Telegram
📖 Description
For beginners: This n8n workflow automatically generates emotionally intense, anime-style visual story scripts using an AI model (Gemini through OpenRouter) and then creates images for each scene using Stablecog. Finally, it sends these images as a multi-photo message to your Telegram chat, complete with scene captions! It's like having an AI director create a silent anime short film and send you the visual storyboard every day.

💻 For tech users: This workflow is triggered by a schedule. It makes an HTTP POST request to OpenRouter's API, leveraging the Gemini 2.5 Flash Lite model to generate a detailed, multi-scene visual story script in a specific format. The generated script is parsed, and individual scene descriptions are extracted. For each scene, an HTTP POST request is made to the Stablecog API to generate an image based on the extracted image prompt. These generated images are then downloaded via subsequent HTTP GET requests. Finally, each image is sent to a specified Telegram chat ID with its corresponding scene caption, creating a visual narrative flow.

⚙️ How It Works (step-by-step with emojis)
🔹 1. Schedule Trigger ⏰
Simple: This node sets the workflow to run automatically at 9 AM every day.

Tech: Uses the Schedule Trigger node with a cron configuration to initiate the workflow daily.

🔹 2. SCRIPT 🤖
Simple: This is where the magic happens! It tells an AI model (Gemini via OpenRouter) to create a unique, emotionally powerful, 60-second anime-style visual story script for you.

Tech: An HTTP Request node sends a POST request to https://openrouter.ai/api/v1/chat/completions. It includes specific headers for Content-Type: application/json and Authorization: Bearer YOUR_OPENROUTER_BEARER_TOKEN, along with a JSON body containing the AI model (google/gemini-2.5-flash-lite-preview-06-17) and a detailed prompt for generating the visual story.

🔹 3. SENDING SCRIPT 📝
Simple: This node takes the full AI-generated story script and saves it into a variable named SCRIPT for later use.

Tech: A Set node assigns the content of $json.body.choices[0].message.content (the AI's response) to a new variable called SCRIPT.

🔹 4. CLEAN SCRIPT 🧼
Simple: This node tidies up the AI's response, breaking the single large script into individual scenes so each part can be processed separately.

Tech: A Code node uses JavaScript to parse the SCRIPT variable. It employs regular expressions to match and extract each SCENE block, creating a new item for each scene with its title and content.

🔹 5. COMBINE SCENE 🔗
Simple: This node formats each individual scene into a readable message, preparing it for display or further processing.

Tech: A Code node iterates through the output of "CLEAN SCRIPT," prepending an emoji and formatting the scene title and content into a single message field.

🔹 6. EXTRACT SCENE 📦
Simple: This node takes the combined scene messages and makes them easily accessible as a list of scenes.

Tech: A Set node takes the message from the previous node and assigns it to a new variable called scenesList.

🔹 7. SEPERATE SCENES ✂️
Simple: This node further refines the scene list, giving each scene a clear number and making its content easily retrievable.

Tech: A Code node processes the scenesList from the "EXTRACT SCENE" node, assigning a sceneNumber (based on the array index) and a sceneMessage (the trimmed content) to each scene.

🔹 8. SCENE 1 - 6 🖼️
Simple: These nodes act as filters, ensuring that only the content for a specific scene number (1 through 6) is passed to the next step. Each scene gets its own processing branch.

Tech: Multiple Code nodes are used, each filtering the input items to only include the one where scene.sceneNumber matches its respective scene (e.g., scene.sceneNumber === 1 for "SCENE 1").

🔹 9. HTTP Request (Image Generation) 🎨
Simple: For each separated scene, this node sends the "Image Prompt" from the script to Stablecog to generate an image that visually represents the scene. There is one of these nodes for each scene (HTTP Request, HTTP Request1, etc.).

Tech: Multiple HTTP Request nodes send POST requests to https://api.stablecog.com/v1/image/generation/create. They include Authorization: Bearer YOUR_STABLECOG_API_KEY and Content-Type: application/json headers. The jsonBody dynamically extracts the "Image Prompt" from the sceneMessage using a regex ={{ $json.sceneMessage.match(/- Image Prompt:(.*?)(- Motion Prompt:|- Visual Effects:|$)/s)?.[1].trim() || '' }} and sets the image width, height, and num_outputs.

🔹 10. IMAGE DOWNLOAD ⬇️
Simple: After an image is generated, this node downloads it so it can be sent to Telegram. There is one of these nodes for each image (IMAGE DOWNLOAD, IMAGE DOWNLOAD1, etc.).

Tech: Multiple HTTP Request nodes perform a GET request to the URL returned by the Stablecog image generation API (={{ $json.body.outputs[0].url }}). The response format is set to file to download the image data.

🔹 11. Send a photo message 📲
Simple: Finally, this node sends the downloaded image to your Telegram chat, with a caption indicating which scene it is. There is one of these nodes for each image (Send a photo message, Send a photo message1, etc.).

Tech: Multiple Telegram nodes with the sendPhoto operation are configured. They use YOUR_TELEGRAM_CHAT_ID and set binaryData to true, sending the image received from the "IMAGE DOWNLOAD" node. The caption is set to the respective scene number (e.g., "SCENE 1").

🔁 Replace the following before running:
🔐 YOUR_OPENROUTER_BEARER_TOKEN
Replace this with your OpenRouter Bearer token in the Authorization header of the SCRIPT node.

🔐 YOUR_STABLECOG_API_KEY
Replace this with your personal Stablecog API key in the Authorization header of all HTTP Request (Image Generation) nodes (HTTP Request, HTTP Request1, etc.).

💬 YOUR_TELEGRAM_CHAT_ID
Insert your personal Telegram chat ID in all Telegram nodes (Send a photo message, Send a photo message1, etc.).

🤖 YOUR_TELEGRAM_BOT_TOKEN
Add your bot token to the Telegram credentials in n8n under the Credentials section. The credential name used in this workflow is SCRIPT FOR VEDIO.

🧪 Testing Instructions
✅ Open n8n at http://localhost:5678 (or your n8n instance URL).
🔧 Click “Import Workflow” in the top right corner and paste the provided JSON.
⚙️ Ensure all placeholder values (e.g., YOUR_OPENROUTER_BEARER_TOKEN, YOUR_STABLECOG_API_KEY, YOUR_TELEGRAM_CHAT_ID) are replaced with your actual credentials and IDs.
▶️ Click “Execute Workflow” in the top right corner to run the entire workflow.
🧪 Alternatively, click “Execute Node” on individual nodes (starting from "Schedule Trigger" or "SCRIPT") to test each step and verify its output.
🪵 Check the execution log (bottom panel in n8n) to troubleshoot any issues or errors.
👀 Expect to see a series of image messages appear in your specified Telegram chat, each with its corresponding scene caption.

💡 Tips for Beginners
💡 Use the “Credentials” tab in n8n to set up your Telegram API key centrally, rather than hardcoding it in each Telegram node.
💡 After executing each step, always check node outputs in the right-hand panel to verify that the data is flowing as expected and that API calls are returning valid responses. This is crucial for debugging!
💡 Adjust the “Schedule Trigger” interval to fit your preferred daily run time for generating new stories.
💡 If image generation fails, check your Stablecog API key and ensure your image prompts are clear and concise for the AI.
💡 If Telegram messages aren't sending, double-check your Telegram Chat ID and Bot Token, and ensure your bot has permission to send messages to the chat.












Canvas


