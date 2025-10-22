# Letta Multi-LLM Frontend

A local browser-based frontend for interacting with Letta agents across multiple LLM providers.

## Features

- 🔄 **Dynamic Model Switching**: Switch between OpenAI, Anthropic, and other LLM providers on the fly
- 💬 **Clean Chat Interface**: Modern, responsive UI for agent conversations
- 🔌 **Real-time Updates**: Automatically updates agent configuration when switching models
- 📊 **Status Display**: Shows current agent ID and active model
- ⌨️ **Keyboard Shortcuts**: Enter to send, Shift+Enter for new lines
- 🎨 **Beautiful Design**: Gradient purple theme with smooth animations

## Quick Start

### Prerequisites

1. **Configure Environment Variables**:
   ```bash
   # Copy the example file
   cp .env.example .env

   # Edit .env and add your API keys
   nano .env  # or use your preferred editor
   ```

2. **Start Letta Server**:
   ```bash
   docker run \
     -v ~/.letta/.persist/pgdata:/var/lib/postgresql/data \
     -p 8283:8283 \
     --env-file .env \
     letta/letta:latest
   ```

   The server will automatically discover all available models based on the API keys in your `.env` file.

3. **Agent Created**: Have an agent created in your Letta server (use the Letta ADE at app.letta.com)

### Usage

1. **Open the frontend** in your browser:
   ```bash
   open letta-frontend.html
   ```
   Or simply double-click `letta-frontend.html`

2. **Wait for initialization**: The frontend will automatically:
   - Connect to your Letta server
   - Load your agent details
   - Fetch all available models

3. **Select a model**: Choose from the dropdown menu to switch LLM providers

4. **Start chatting**: Type your message and press Enter!

## Configuration

### Frontend Configuration

The frontend is pre-configured with your Letta server details:

```javascript
const CONFIG = {
    LETTA_BASE_URL: 'http://localhost:8283',
    LETTA_TOKEN: 'yourpassword',
    LETTA_API_TOKEN: 'sk-let-...',
    LETTA_PROJECT: 'default',
    LETTA_AGENT_ID: 'agent-a0b842d2-f808-47bd-b477-bb85ca1e7d00'
};
```

To change these settings, edit the `CONFIG` object in `letta-frontend.html`.

### Server Configuration (.env)

The Letta server uses environment variables for LLM provider API keys. Edit `.env` to add your keys:

```bash
# .env file
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...
GOOGLE_AI_API_KEY=...
OLLAMA_BASE_URL=http://host.docker.internal:11434
```

**Important:**
- The frontend **doesn't need** API keys - it only talks to your Letta server
- The Letta server handles all LLM provider communication
- Available models are automatically discovered from your configured API keys
- See `.env.example` for all supported providers

## How It Works

### Model Switching (Persistent!)
When you select a new model from the dropdown:

1. Frontend sends a PATCH request to `/v1/agents/{agent_id}`
2. Updates the agent's `llm_config.model` parameter **in the database**
3. Agent immediately starts using the new model for responses
4. UI updates to show the current active model

**Important:** Model changes are **permanent** until you switch again! This means:
- ✅ Persists across browser refreshes
- ✅ Persists if you close and reopen the frontend
- ✅ Visible in the Letta ADE
- ✅ Affects all API calls to this agent
- ✅ Only changes when you explicitly switch models

This allows you to:
- Set a default model for your agent
- Switch to cheaper models for testing
- Use more powerful models for complex tasks
- Compare different providers on the same conversation

### Message Flow
1. User types message and clicks Send (or presses Enter)
2. Message sent via POST to `/v1/agents/{agent_id}/messages`
3. Agent processes with current LLM provider
4. Response messages displayed in chat interface

### Model Loading
- On startup, fetches all models from `/v1/models`
- Groups models by provider (OpenAI, Anthropic, etc.)
- Creates organized dropdown with provider categories

## API Endpoints Used

- `GET /v1/agents/{agent_id}` - Load agent details
- `PATCH /v1/agents/{agent_id}` - Update agent model
- `GET /v1/models` - List available models
- `POST /v1/agents/{agent_id}/messages` - Send messages

## Troubleshooting

### Can't connect to Letta server
- Ensure server is running on `http://localhost:8283`
- Check that CORS is enabled on your server
- Verify the password is correct

### No models showing up
- Check your LLM provider API keys are set in the Docker environment
- Click "Refresh Models" button
- Check browser console for errors

### Messages not sending
- Verify agent ID is correct
- Check browser console for API errors
- Ensure agent hasn't reached token limits

## Browser Compatibility

- Chrome/Edge: ✅ Full support
- Firefox: ✅ Full support
- Safari: ✅ Full support
- Mobile browsers: ✅ Responsive design

## Development

The frontend is a single HTML file with embedded CSS and JavaScript. To modify:

1. Open `letta-frontend.html` in your editor
2. Make changes to HTML, CSS, or JavaScript sections
3. Refresh browser to see changes
4. No build process required!

## Next Steps

Consider extending the frontend with:

- Message history persistence
- Conversation export/import
- Advanced agent settings UI
- Multiple agent support
- Streaming responses
- File upload capabilities

## License

This project is part of the raspy2 repository.
