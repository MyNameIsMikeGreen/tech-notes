Hosting a React app with Cloudflare Pages
=========================================

[Cloudflare](https://www.cloudflare.com/) offers a way to host React apps on their network using [Cloudflare Pages](https://pages.cloudflare.com/).

Official instructions can be found at: https://developers.cloudflare.com/pages/framework-guides/deploy-a-react-application

# Deploying a Minimal App
* Create an app using [`create-react-app`](https://create-react-app.dev/)
* Host this app on GitHub.
* Log in to Cloudfront (Or create an account).
* Follow instructions to create a new deployment.
* App will be available at `https://your-app-name.pages.dev`

# Adding Custom Domains
Without explicitly allowing specific domains from within the Cloudfront interface, requests to the app via a custom domain (regardless of DNS records) will likely result in HTTP 522 errors. Do these steps to get a custom domain correctly pointing to a deployment:

* Purchase a domain name.
* (Optionally/Easiest) Set the domain's nameservers to Cloudfront.
* Open the Cloudfront Pages management interface.
* Explicitly add an entry in the `Custom Domains` tab for all domains and sub-domains that should direct traffic to the deployment.
  * If using Cloudfront as the DNS provider, use the auto-configuration.
  * If using another DNS provider, follow the instructions to set DNS up correctly.

The final step must be completed even if the DNS records have been set and look correct. Without completing the wizard, the application will actively refuse connections using custom domains.
