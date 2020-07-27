# Yet Another Browser Mud

This is a playful text-based online social chat space. You can think of it as a hybrid between communication apps like Slack and Discord and traditional text-based online game spaces such as MUDs and MOOs.

It is primarily being built for [Roguelike Celebration 2020](https://roguelike.club), but can hopefully eventually be repurposed for other events or communities.

On the backend, it's powered by a serverless system made up of Azure Functions, Azure SignalR Service, and a Redis instance (currently provided by Azure Cache for Redis).

On the frontend, it's a rich single-page webapp built in TypeScript and React, using the Flux architecture via the `useContext` React hook.

## Setting up a development environment

### Frontend Dev

1. Clone this repo

2. Run `npm install`

3. `npm run dev` will start a local development environment (at `http://localhost:1234` by default). It auto-watches changes to HTML, CSS, and JS/TS code, and attempts to live-reload any connected browser instances.

4. `npm run build` will generate a bundled version of the webapp for distribution. If you're planning to push changes back to this repo for use in its official deployment, don't worry about this; a GitHub Actions CI pipeline builds and deploys the main branch on every push.

### Backend Dev

1. Deploy the `server` folder to a new Azure Function App instance you control. I recommend using VS Code and the VS Code Azure Functions extension. See the "Publish the project to Azure" section of [this tutorial](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript?WT.mc_id=github-code-emwalker) for details. You can also use the Azure CLI or any other method.

2) In the Azure Portal, sign up for a new [Azure SignalR Service](https://docs.microsoft.com/en-us/azure/azure-signalr/signalr-overview?WT.mc_id=github-code-emwalker) instance. For development purposes, you can probably start with the free tier.

3) Grab the connection string from your Azure SignalR Service instance. Back in the settings for your Function App, go to the Configuration tab and add a Application Setting with the key `AzureSignalRConnectionString`

4) Set up an [Azure Cache for Redis](https://docs.microsoft.com/en-us/azure/azure-cache-for-redis/cache-overview?WT.mc_id=github-code-emwalker) instance. Again, the cheapest tier is likely acceptable for testing purposes. You could theoretically use an alternative Redis provider, as nothing about our use is Azure-specific, but I have not tried this.

5) As above, you want to take your Redis access key, the hostname, and the port, and add them as Application settings to the Function App with the keys `RedisKey` and `RedisHostname`, `RedisPort`.

6) Set up Twitter authentication. In the Azure Portal, pull up the Function App and go to "Authentication". In another window, go to https://developer.twitter.com/apps and register a new Twitter developer application. You will need to paste the consumer key and secret from Twitter into the Azure setup screen for Twitter. The callback URL to enter in the Twitter app is `https://your-function-app.azurewebsites.net/.auth/login/twitter/callback`, swapping in the URL hostname of your Function app.

7) Set up CORS in the Azure Portal page for the Function app. There's a "CORS" menu item on the left. Allow `http://localhost:1234` for local development, as well as whatever URLs you're using for a production version of the frontend.

8) In `src/config.ts` in this repo, update the hostname to point to your own Function App instance.
