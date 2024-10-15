# immerse-socal
Here are my code samples from my presentation - Demo: Three (Quick and Easy!) Paths to App Modernization

Rehost
- No code samples

Replatform
- Loop I used to test Load Balancing results in Terminal:

`while true; do curl -s https://lb.christoroberts.com | grep -i '<title>' | sed -e 's/<title>//I' -e 's/<\/title>//I'; done`

Rehost
- Chat bot js in format that works as a snippet. Just replace the YOUR_API_KEY with your own.

```export default {
    async fetch(request) {
        // Fetch the original response
        let response = await fetch(request);

        // Clone the headers and check if the content is HTML
        let newHeaders = new Headers(response.headers);

        if (newHeaders.get("Content-Type") && newHeaders.get("Content-Type").includes("text/html")) {
            // Read the HTML body
            let body = await response.text();

            // Define the chatbot HTML and JavaScript to inject
            const chatbotScript = `
        <script>
        (function() {
            // Create chatbot container dynamically
            const chatbotContainer = document.createElement('div');
            chatbotContainer.id = 'chatbot-container';
            chatbotContainer.innerHTML = \`
                <div id="chatbot-header">Ask Me Anything!</div>
                <div id="chatbot-body">
                    <div id="chatbot-messages"></div>
                    <input type="text" id="chatbot-input" placeholder="What's up?" />
                    <button id="chatbot-send-btn">Send</button>
                </div>
            \`;
            document.body.appendChild(chatbotContainer);

            // Inject CSS for styling
            const style = document.createElement('style');
            style.innerHTML = \`
                #chatbot-container {
                    position: fixed;
                    bottom: 20px;
                    right: 20px;
                    width: 300px;
                    border-radius: 10px;
                    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
                    font-family: Arial, sans-serif;
                    background-color: #f9f9f9;
                    border: 2px solid #f48024;
                    z-index: 9999; /* Ensure it's on top of other elements */
                }
                #chatbot-header {
                    background-color: #f48024;
                    color: white;
                    padding: 10px;
                    text-align: center;
                    border-top-left-radius: 10px;
                    border-top-right-radius: 10px;
                }
                #chatbot-body {
                    padding: 10px;
                }
                #chatbot-messages {
                    height: 300px;
                    overflow-y: auto;
                    border: 1px solid #ddd;
                    margin-bottom: 10px;
                    padding: 5px;
                    background-color: white;
                    color: black; /* Ensure message text is visible */
                }
                #chatbot-input {
                    width: calc(100% - 60px);
                    padding: 10px;
                    border: 1px solid #ddd;
                    border-radius: 5px;
                    margin-right: 10px;
                    color: black; /* Ensure text typed into input is visible */
                    background-color: white;
                }
                #chatbot-send-btn {
                    padding: 10px 15px;
                    background-color: #f48024;
                    color: white;
                    border: none;
                    border-radius: 5px;
                    cursor: pointer;
                }
            \`;
            document.head.appendChild(style);

            // Function to handle sending a message and getting a response from ChatGPT
            async function handleSendMessage() {
                const userMessage = document.getElementById('chatbot-input').value;
                if (userMessage.trim() === '') return;

                // Append user message to messages container
                const messagesContainer = document.getElementById('chatbot-messages');
                const userMessageElem = document.createElement('div');
                userMessageElem.innerHTML = \`<strong>You:</strong> \${userMessage}\`;
                messagesContainer.appendChild(userMessageElem);
                document.getElementById('chatbot-input').value = ''; // Clear input field

                // Scroll to the bottom of the messages container
                messagesContainer.scrollTop = messagesContainer.scrollHeight;

                // Fetch response from ChatGPT API
                try {
                    const response = await fetch('https://api.openai.com/v1/chat/completions', {
                        method: 'POST',
                        headers: {
                            'Content-Type': 'application/json',
                            'Authorization': \`Bearer YOUR_API_KEY\` // Replace with your actual API key
                        },
                        body: JSON.stringify({
                            model: "gpt-3.5-turbo",
                            messages: [{ role: "user", content: userMessage }]
                        })
                    });

                    const data = await response.json();
                    const botMessage = data.choices[0].message.content;

                    // Append bot response to messages container
                    const botMessageElem = document.createElement('div');
                    botMessageElem.innerHTML = \`<strong>Chatty McChat Bot:</strong> \${botMessage}\`;
                    messagesContainer.appendChild(botMessageElem);

                    // Scroll to the bottom of the messages container
                    messagesContainer.scrollTop = messagesContainer.scrollHeight;

                } catch (error) {
                    console.error('Error fetching ChatGPT response:', error);
                    const errorMessageElem = document.createElement('div');
                    errorMessageElem.innerHTML = \`<strong>Chatty McChat Bot:</strong> Error retrieving response.\`;
                    messagesContainer.appendChild(errorMessageElem);
                }
            }

            // Add event listener to send button
            document.getElementById('chatbot-send-btn').addEventListener('click', handleSendMessage);

            // Add event listener to trigger "Send" when Enter is pressed
            document.getElementById('chatbot-input').addEventListener('keypress', function (e) {
                if (e.key === 'Enter') {
                    handleSendMessage();
                }
            });
        })();
        </script>
      `;

            // Inject the chatbot script into the HTML before </body>
            body = body.replace('</body>', chatbotScript + '</body>');

            // Return the modified HTML response
            return new Response(body, {
                status: response.status,
                statusText: response.statusText,
                headers: newHeaders
            });
        }

        // Return the original response for non-HTML requests
        return response;
    },
};```
