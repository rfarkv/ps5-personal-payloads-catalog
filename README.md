# PS5 Personal Payloads Catalog

> **About the Claude badge in Contributors:** yes, the mark of the beast is there. But rest assured, I'm a developer myself and only used it to speed things up. The ideas and decisions were mine, I reviewed every change, and nothing was done blindly.

Your own **payload repository** for the **PS5 Payload Manager (`pldmgr`)**. In this README, "payload repository" always means the `payloads.json` file you add to the Payload Manager. "GitHub repository" always means a project on GitHub.

You choose which GitHub repositories to track, and a GitHub Action keeps `payloads.json` pointing to the latest `.elf`/`.bin` release of each one. The PS5 Payload Manager then shows the new versions, so you can update your payloads directly on the PS5. There is no need to download files or copy them over by hand.

## Quick start

1. **Fork** this GitHub repository.
2. In your fork, open the **Actions** tab and click the button to enable workflows.
3. Go to **Settings → Actions → General**, and under **Workflow permissions** select **Read and write permissions**. Save. Then go back to **Actions**, open **Update payloads.json** and click **Run workflow** once.
4. Go to **Settings → Pages**, choose **Deploy from a branch**, branch `main`, folder `/ (root)`. Save and wait about a minute.
5. Create a [GitHub token](#github-token) with access to only your fork.
6. Open `https://YOUR-USER.github.io/ps5-personal-payloads-catalog/manage/`, click **Set GitHub token**, paste it and click **Save**. Leave **Code source** on **Upstream** ([what's the difference?](#code-source-upstream-or-local)).
7. Remove the example payloads you don't want, then click **+ Add** to [add yours](#adding-payloads).
8. [Add your payload repository URL to the PS5 Payload Manager](#using-it-in-the-ps5-payload-manager).

That's it. New releases now show up in the Payload Manager on their own.

## How it works

- `repos/` lists the GitHub repositories you track, one file per GitHub repository.
- Every 6 hours, and whenever `repos/` changes, a GitHub Action checks the latest release of each one, takes its `.elf` or `.bin` file and rebuilds `payloads.json`.
- GitHub Pages serves `payloads.json` and the management page.

You never edit `payloads.json` yourself. It is filled in automatically with each GitHub repository's information: name, description, file name, version and download link from its latest release.

## Adding payloads

### Using the page

Click **+ Add**, paste one or more GitHub repository URLs (one per line), click **Check**, then **Add**. All of them are saved in a single commit. The page needs a saved [GitHub token](#github-token); without one it is read-only.

### Manually

Create a file in the `repos/` folder (for example `owner__repo.txt`) containing the GitHub repository URL, `https://github.com/owner/repo`. A single file can also list several GitHub repositories, one per line. Commit to `main` and the Action updates `payloads.json`.

Either way, the new payload shows up within a few minutes. The GitHub repository needs a release with a `.elf` or `.bin` file. If payloads stay **pending** (or **pending removal**), the Action isn't running: the page shows a warning at the top when it is disabled or its last run failed.

Your fork starts with the original list of tracked GitHub repositories. Remove the ones you don't want from the management page.

## Using it in the PS5 Payload Manager

In the Payload Manager, add this URL as a repository:

```
https://YOUR-USER.github.io/ps5-personal-payloads-catalog/payloads.json
```

The management page also shows this URL with a **Copy** button. Whenever a tracked GitHub repository publishes a new release, the payload repository is updated automatically within 6 hours, and the Payload Manager will offer the new version. To update it right away, click **Check for new releases** on the management page.

## GitHub token

The management page needs a [Fine-Grained Personal Access Token](https://github.com/settings/personal-access-tokens/new) to save changes to your fork:

- **Repository access:** *Only select repositories*, with only your fork selected.
- **Permissions → Contents:** *Read and write*, to add and remove payloads.
- **Permissions → Actions:** *Read and write*, for the **Check for new releases** button.

Already have a token without the Actions permission? [Edit it](https://github.com/settings/personal-access-tokens) on GitHub and add it; the saved token keeps working.

You only enter it once per browser. It stays saved there until it expires or you remove it, and it is only ever sent to the GitHub API.

## Code source: upstream or local

`config.json` in your fork sets where the management page and the Action get their code from:

```json
{ "source": "upstream" }
```

- **`upstream`** (default, recommended): uses the released version from [rfarkv/ps5-personal-payloads-catalog](https://github.com/rfarkv/ps5-personal-payloads-catalog), so your fork gets improvements without syncing.
- **`local`**: uses only the code in your fork. Everything is static and self-contained; to get improvements, use **Sync fork** on GitHub.

Either way, everything runs in your own browser and your own GitHub repository. With `upstream`, only the code is loaded from there: your token, your list and your commits never go to that GitHub repository.

To switch, open the management page, click **Set GitHub token** (or **Token saved**) and choose under **Code source**, or edit `config.json` directly. It takes effect after the next Pages deploy (about a minute).
