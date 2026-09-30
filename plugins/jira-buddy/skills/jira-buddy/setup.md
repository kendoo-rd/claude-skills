# First-time setup

Run this when `.claude/jira-buddy.json` does not exist in the project, or when
the user asks to change the settings.

## Steps

1. **Find the site.** Call `getAccessibleAtlassianResources`. If there is one
   site, use it. If there are several, ask the user to pick. Note the site
   address and its `cloudId`.
2. **Find the projects.** Call `getVisibleJiraProjects`. Sort them into:
   - normal Jira projects (software or business),
   - Product Discovery projects (project type `product_discovery`),
   - Service Management projects (project type `service_desk`).
3. **Ask the user to choose**, with choices, one topic at a time:
   - Which projects this project's tickets go to, and what each is for
     (for example "bugs", "new features", "support requests"). One is the default.
   - The default epic for each project, if any. Offer the open epics found
     with `searchJiraIssuesUsingJql`
     (`project = KEY AND issuetype = Epic AND statusCategory != Done`).
   - Which Product Discovery project to search for ideas, if any.
   - Which Service Management project to search for known issues, if any.
   - Confluence: search no spaces, some spaces (pick from
     `getConfluenceSpaces`), or all spaces.
   - A default assignee, or none.
4. **Show the settings file** and ask for a yes.
5. **Save it** at `.claude/jira-buddy.json` in the project root. Tell the user
   the team can commit it so everyone shares the same settings.

Skip any question with an obvious answer (for example, only one project).

## Settings file

```json
{
  "site": "<your-site>.atlassian.net",
  "cloudId": "00000000-0000-0000-0000-000000000000",
  "projects": [
    {
      "key": "<PROJECT-KEY>",
      "use_for": "bugs and new features",
      "default_epic": "<EPIC-KEY>",
      "default": true
    },
    {
      "key": "<OTHER-PROJECT-KEY>",
      "use_for": "support requests",
      "default_epic": null
    }
  ],
  "components": [],
  "default_assignee": null,
  "discovery_project": "<DISCOVERY-PROJECT-KEY>",
  "service_desk_project": "<SERVICE-PROJECT-KEY>",
  "confluence_spaces": ["<SPACE-KEY>"]
}
```

| Field | Meaning |
| --- | --- |
| `site`, `cloudId` | Which Atlassian site to use. |
| `projects` | Where tickets go. `use_for` helps pick the right one. |
| `default_epic` | Parent epic when nothing better fits. |
| `components` | Allowed components, if the team uses them. Empty means skip. |
| `default_assignee` | Account id, or `null` for unassigned. |
| `discovery_project` | Product Discovery project to search for ideas, or `null`. |
| `service_desk_project` | Service Management project to search for known issues, or `null`. |
| `confluence_spaces` | `null`: do not search Confluence. `["KEY", ...]`: search only these spaces. `"all"`: search every space the user can see. |

The file holds no secrets. Sign-in is handled by the Atlassian connector, not
by this file.

## Routing a ticket

Pick the project whose `use_for` matches the request best. If two match, ask
the user with both as choices. Put the ticket under the best-fitting open
epic (see `related-items.md`), or the project's `default_epic`.
