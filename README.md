# n8n-ollama-automated-workflow
Local AI workflow in n8n using Ollama as the LLM backend

1. Clone & Initialize Starter Kit
Clone the Self-Hosted AI Starter Kit locally.

2. Set up n8n
Follow the kit instructions to configure n8n.

3. Start the Stack - This launches the AI stack: n8n, ai-worker, Postgres, and Qdrant.
Run:
docker compose up

4. Install Ollama (Mac)
Since this was set up on a Mac, install Ollama (https://ollama.com/download) and launch its GUI app.

5. Verify Ollama
Check it is running:
http://localhost:11434

6. Pull a Model
Download a model (e.g. llama3) with:
ollama run llama3

7. Configure .env
Ensure that .env is updated so that the AI worker (in Docker) can talk to Ollama (on the Mac).

8. Restart the Stack
Start the kit:
docker compose up
Or, if it already appears in Docker Desktop, click Play.

9. Verify n8n UI
Go to:
http://localhost:5678

10. Update Local Ollama Credential
In n8n, update Local Ollama Service credential to:
http://host.docker.internal:11434/
This tells n8n inside Docker to talk to Ollama on our Mac.

11. Run Everything Together
Ollama running locally, Starter kit stack running in Docker
Access n8n at http://localhost:5678

12. Modify and Test Workflow
Open a workflow, wire up the model, and start testing!


Notes:
1. To make sure that ollama is working fine, I had to check for its model. It didn't exist as first, but I later on pulled it in my local and then tried to re-run the setup. It ran successfully then.
To check for the ollama models in my local, I used "ollama list", it gave me the list of all the models I had on my machine
2. To update the credentials in n8n, 
first of all why it was needed - the AI nodes of the workflow used the credential system to know wehere the LLM is hosted. Now, if the URL was "http://localhost:11434", then it would fail everytime.
Because n8n is running inside Docker, and when we are inside a container, localhost means we are inside a container, not on mac. So, when n8n tries to access the above link, it would look for Ollama inside it's own container (and it doesn't exist there unfortunately).
To solve this, Docker actually provides special hostnames "host.docker.internal", which let's the container reach the host machine. (That means we are telling it to now talk to port 11434 which is running on our local) 
And, when we update the credential->local ollama service url to "http://host.docker.internal:11434/", connection becomes successful.
3. When I ran the model, it failed initially, because the fallback model's link was not connected to anything. Make sure to connect a fallback model to a model or another model. (Fallback model as the name says in case the initial model fails (timeout, error, no output) then the request diverts to this model)