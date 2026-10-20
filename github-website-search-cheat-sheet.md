# GitHub Website Search Cheat Sheet

## Search filters, qualifiers, and examples --- no terminal commands

Use the **search bar on GitHub.com**. Most filters can be combined to
narrow results. Replace example words, usernames, repository names,
branches, and dates with your own values.

Repository used in examples: `dgk45/SampleProgrammers`

> **Important:** Some qualifiers work only for a specific result type.
> For example, `is:pr` is for pull requests, `is:issue` is for issues,
> and `language:C#` is commonly used when searching repositories or
> code. GitHub search behavior can vary by search type.

------------------------------------------------------------------------

## 1. Basic search patterns

  --------------------------------------------------------------------------
  What you want                       Search in GitHub
  ----------------------------------- --------------------------------------
  Search for a word or phrase         `login`

  Search for an exact phrase          `"login feature"`

  Search within a specific repository `repo:dgk45/SampleProgrammers login`

  Exclude a word                      `login -password`

  Search for either term              `login OR authentication`

  Search for both terms               `login authentication`

  Search for a phrase but exclude     `"login feature" -bug`
  another                             

  Search across a particular owner's  `user:dgk45 login`
  repositories                        

  Search within a particular          `org:YOUR-ORG login`
  organization                        
  --------------------------------------------------------------------------

**Tips** - Use quotes for an exact phrase. - Put a minus sign directly
before a term to exclude it, such as `-draft`. - Combine qualifiers to
narrow the result set. - Use the correct repository owner/name in
`repo:OWNER/REPOSITORY`.

------------------------------------------------------------------------

## 2. Pull request (PR) searches

Start with `is:pr` to search pull requests.

  ---------------------------------------------------------------------------------------------------
  Goal                                Search query
  ----------------------------------- ---------------------------------------------------------------
  All PRs                             `is:pr`

  PRs mentioning login                `is:pr login`

  PR title contains login             `is:pr in:title login`

  PR body contains login              `is:pr in:body login`

  Title or body contains login        `is:pr in:title,body login`

  PRs in your repository              `repo:dgk45/SampleProgrammers is:pr`

  Open PRs                            `is:pr is:open`

  Closed PRs                          `is:pr is:closed`

  Merged PRs                          `is:pr is:merged`

  Draft PRs                           `is:pr is:draft`

  PRs not in draft state              `is:pr -is:draft`

  PRs targeting master                `is:pr base:master`

  PRs from feature-login branch       `is:pr head:feature-login`

  PRs with a bug label                `is:pr label:bug`

  PRs without a label                 `is:pr no:label`

  PRs assigned to you                 `is:pr assignee:@me`

  PRs created by you                  `is:pr author:@me`

  PRs you reviewed                    `is:pr reviewed-by:@me`

  PRs awaiting your review            `is:pr review-requested:@me`

  PRs involving a particular user     `is:pr involves:USERNAME`

  PRs updated recently                `is:pr updated:>=2026-10-01`

  PRs created in a date range         `is:pr created:2026-09-01..2026-10-10`

  Merged PR title contains login in   `repo:dgk45/SampleProgrammers is:pr is:merged in:title login`
  your repo                           

  Open PR title contains bug but not  `is:pr is:open in:title bug -login`
  login                               
  ---------------------------------------------------------------------------------------------------

### PR review-related qualifiers

  Goal                          Search query
  ----------------------------- ----------------------------------
  PRs with approved review      `is:pr review:approved`
  PRs with changes requested    `is:pr review:changes_requested`
  PRs with review required      `is:pr review:required`
  PRs with no review decision   `is:pr review:none`

*Review qualifiers and their availability can depend on GitHub's current
search support and the repository's review state.*

------------------------------------------------------------------------

## 3. Issue searches

GitHub's issue search includes issues and PRs unless you specify a type.
Use `is:issue` when you want issues only.

  -------------------------------------------------------------------------------
  Goal                                Search query
  ----------------------------------- -------------------------------------------
  Issues only                         `is:issue`

  Issues mentioning crash             `is:issue crash`

  Issue title contains crash          `is:issue in:title crash`

  Issue body contains timeout         `is:issue in:body timeout`

  Open issues                         `is:issue is:open`

  Closed issues                       `is:issue is:closed`

  Issues created by you               `is:issue author:@me`

  Issues assigned to you              `is:issue assignee:@me`

  Issues mentioning you               `is:issue mentions:@me`

  Issues involving a user             `is:issue involves:USERNAME`

  Issues with a label                 `is:issue label:bug`

  Issues with either of two labels    `is:issue label:bug label:urgent`

  Issues with no label                `is:issue no:label`

  Issues with no assignee             `is:issue no:assignee`

  Issues with no milestone            `is:issue no:milestone`

  Issues created after a date         `is:issue created:>=2026-10-01`

  Issues updated in a date range      `is:issue updated:2026-09-01..2026-10-10`

  Issues closed in a date range       `is:issue closed:2026-09-01..2026-10-10`

  Issues with a comment               `is:issue comments:>0`

  Issues with many comments           `is:issue comments:>=10`
  -------------------------------------------------------------------------------

------------------------------------------------------------------------

## 4. Code search

Use code search when looking for text inside source files. Select
**Code** in the GitHub search results if needed.

  -------------------------------------------------------------------------------------------------
  Goal                                Search query
  ----------------------------------- -------------------------------------------------------------
  Find a symbol or text               `Program.cs`

  Search code in one repository       `repo:dgk45/SampleProgrammers login`

  Search only C# files                `repo:dgk45/SampleProgrammers extension:cs login`

  Search a specific filename          `repo:dgk45/SampleProgrammers filename:Program.cs`

  Search files in a folder            `repo:dgk45/SampleProgrammers path:SampleProgrammers login`

  Search a method name                `repo:dgk45/SampleProgrammers "Main("`

  Search for a comment                `repo:dgk45/SampleProgrammers "// TODO"`

  Find a configuration file           `repo:dgk45/SampleProgrammers filename:appsettings.json`

  Find project files                  `repo:dgk45/SampleProgrammers extension:csproj`

  Find solution files                 `repo:dgk45/SampleProgrammers extension:sln`

  Exclude generated files by          `repo:dgk45/SampleProgrammers login -extension:designer.cs`
  extension                           
  -------------------------------------------------------------------------------------------------

**Code search notes** - Code search has its own supported qualifiers and
syntax; not every issue/PR qualifier works in code search. - `filename:`
and `path:` are useful for narrowing by file name and folder. -
`extension:` narrows by file extension. - Private repositories appear
only if your account has permission to access them. - Some code search
features may require signing in.

------------------------------------------------------------------------

## 5. Commit searches

Use commit search to find commits by message, author, or repository.

  --------------------------------------------------------------------------------------------
  Goal                                Search query
  ----------------------------------- --------------------------------------------------------
  Search commits                      `is:commit login`

  Search commit messages in your repo `repo:dgk45/SampleProgrammers is:commit login`

  Commit message contains fix         `repo:dgk45/SampleProgrammers is:commit fix`

  Commits by a user                   `is:commit author:USERNAME`

  Commits authored by you             `is:commit author:@me`

  Commits committed by a user         `is:commit committer:USERNAME`

  Commits in a date range             `is:commit committer-date:2026-09-01..2026-10-10`

  Commits after a date                `is:commit author-date:>=2026-10-01`

  Search commits in a repository and  `repo:dgk45/SampleProgrammers is:commit branch:master`
  branch                              
  --------------------------------------------------------------------------------------------

*Commit search qualifiers may differ from the qualifiers used for PRs
and issues. Search results depend on GitHub's indexed commit data.*

------------------------------------------------------------------------

## 6. Repository searches

Choose **Repositories** as the result type or use repository qualifiers.

  -----------------------------------------------------------------------
  Goal                                Search query
  ----------------------------------- -----------------------------------
  Find repositories by name or        `dotnet sample`
  description                         

  Repositories owned by a user        `user:dgk45`

  Repositories in an organization     `org:YOUR-ORG`

  Repositories written in C#          `language:C#`

  Repositories with a topic           `topic:dotnet`

  Repositories with at least 100      `stars:>=100`
  stars                               

  Repositories with fewer than 50     `stars:<50`
  stars                               

  Repositories with at least 20 forks `forks:>=20`

  Recently updated repositories       `pushed:>=2026-10-01`

  Repositories created in a date      `created:2025-01-01..2026-10-10`
  range                               

  Public repositories                 `is:public`

  Private repositories you can access `is:private`

  Archived repositories               `archived:true`

  Non-archived repositories           `archived:false`

  Repositories with a license         `license:mit`

  Repositories with a specific topic  `topic:dotnet language:C#`
  and language                        
  -----------------------------------------------------------------------

**Repository search note:** `stars:`, `forks:`, `language:`, `topic:`,
`license:`, `archived:`, and `pushed:` are primarily repository-search
filters.

------------------------------------------------------------------------

## 7. User searches

Choose **Users** in the results or use user-related filters where
supported.

  Goal                                              Search query
  ------------------------------------------------- --------------------
  Find a username or profile                        `USERNAME`
  Find users by location                            `location:India`
  Find users by language                            `language:C#`
  Find users with at least 100 followers            `followers:>=100`
  Find users with at least 50 public repositories   `repos:>=50`
  Find users with a name or profile keyword         `developer dotnet`

*User search has its own supported qualifiers. A qualifier that works
for repositories or issues may not work for users.*

------------------------------------------------------------------------

## 8. Labels, milestones, and assignment

These are most useful in issue and PR searches.

  Goal                                    Search query
  --------------------------------------- -------------------------------------
  Bug-labeled issues                      `is:issue label:bug`
  Enhancement-labeled issues              `is:issue label:enhancement`
  Two labels required                     `is:issue label:bug label:urgent`
  Label name contains a space             `is:issue label:"good first issue"`
  No label                                `is:issue no:label`
  Assigned to a specific person           `is:issue assignee:USERNAME`
  Unassigned issues                       `is:issue no:assignee`
  Issues in a milestone                   `is:issue milestone:"Version 2"`
  Issues without a milestone              `is:issue no:milestone`
  Issues created by a particular person   `is:issue author:USERNAME`
  Issues mentioning a person              `is:issue mentions:USERNAME`

------------------------------------------------------------------------

## 9. Date and count filters

Date and count qualifiers are especially useful for issues and PRs.

  Goal                         Search query
  ---------------------------- -------------------------------------------
  Created on or after a date   `is:pr created:>=2026-10-01`
  Created before a date        `is:pr created:<2026-10-10`
  Updated in a date range      `is:issue updated:2026-09-01..2026-10-10`
  Closed on or after a date    `is:issue closed:>=2026-10-01`
  At least 5 comments          `is:issue comments:>=5`
  No comments                  `is:issue comments:0`
  At least 3 reactions         `is:issue reactions:>=3`
  At least 2 interactions      `is:issue interactions:>=2`

Use ISO-style dates: `YYYY-MM-DD`. Date support varies by qualifier and
result type.

------------------------------------------------------------------------

## 10. Combining filters

You can combine several qualifiers in one search.

  -----------------------------------------------------------------------------------------------------------
  Goal                                Search query
  ----------------------------------- -----------------------------------------------------------------------
  Open PRs in your repository with    `repo:dgk45/SampleProgrammers is:pr is:open in:title login`
  login in title                      

  Merged PRs targeting master         `repo:dgk45/SampleProgrammers is:pr is:merged base:master`

  Your PRs created recently           `repo:dgk45/SampleProgrammers is:pr author:@me created:>=2026-10-01`

  Unassigned bug issues               `repo:dgk45/SampleProgrammers is:issue is:open label:bug no:assignee`

  C# code in a specific repository    `repo:dgk45/SampleProgrammers language:C# login`

  Repositories using .NET with many   `topic:dotnet stars:>=100`
  stars                               

  Merged PRs with either keyword      `is:pr is:merged in:title login OR authentication`

  Find login-related items but        `login -documentation`
  exclude documentation               

  Search a phrase in PR titles and    `is:pr in:title "login feature" -is:draft`
  exclude drafts                      
  -----------------------------------------------------------------------------------------------------------

**OR caution:** Use uppercase `OR` to express alternatives. When
combining `OR` with many other filters, try a simpler query first, then
add more qualifiers.

------------------------------------------------------------------------

## 11. Common qualifier glossary

  -----------------------------------------------------------------------
  Qualifier                           Meaning / typical use
  ----------------------------------- -----------------------------------
  `repo:OWNER/REPO`                   Limit results to one repository

  `user:USERNAME`                     Limit to repositories owned by a
                                      user, or users where supported

  `org:ORGNAME`                       Limit to an organization

  `is:pr`                             Pull requests

  `is:issue`                          Issues

  `is:open`                           Open issues or PRs

  `is:closed`                         Closed issues or PRs

  `is:merged`                         Merged PRs

  `is:draft`                          Draft PRs

  `in:title`                          Search title text

  `in:body`                           Search body text

  `in:title,body`                     Search both title and body

  `author:USERNAME`                   Author

  `assignee:USERNAME`                 Assignee

  `mentions:USERNAME`                 Mentions a user

  `involves:USERNAME`                 Items involving a user

  `reviewed-by:USERNAME`              PRs reviewed by a user

  `review-requested:USERNAME`         PRs requesting review from a user

  `base:BRANCH`                       PR target branch

  `head:BRANCH`                       PR source branch

  `label:NAME`                        Label

  `milestone:NAME`                    Milestone

  `no:label`                          No label

  `no:assignee`                       No assignee

  `created:DATE`                      Creation date

  `updated:DATE`                      Last update date

  `closed:DATE`                       Close date

  `comments:NUMBER`                   Comment count

  `stars:NUMBER`                      Repository star count

  `forks:NUMBER`                      Repository fork count

  `language:LANGUAGE`                 Language filter, mainly
                                      repositories/code

  `topic:TOPIC`                       Repository topic

  `filename:NAME`                     Code filename

  `path:PATH`                         Code path

  `extension:EXT`                     File extension

  `archived:true/false`               Repository archived state
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 12. Quick practice tasks

Try these directly in the GitHub.com search bar:

1.  Find all PRs in `dgk45/SampleProgrammers`.
2.  Find merged PRs whose title contains `login`.
3.  Find open issues with the `bug` label.
4.  Find PRs targeting `master`.
5.  Find C# files containing `login` in this repository.
6.  Find commits mentioning `revert`.
7.  Find repositories tagged with `dotnet` and written in C#.
8.  Find issues updated during the last month by setting the appropriate
    date range.
9.  Find your own PRs using `author:@me`.
10. Find PRs waiting for your review using `review-requested:@me`.

------------------------------------------------------------------------

## 13. Troubleshooting search results

-   **Too many results:** Add `repo:OWNER/REPO`, `is:pr`, `is:issue`,
    `in:title`, or a date filter.
-   **No results:** Remove one qualifier at a time; check spelling,
    branch names, labels, and date format.
-   **Wrong result type:** Use `is:pr` or `is:issue`, or select the
    desired tab in GitHub search results.
-   **Searching PR titles:** Include `is:pr in:title`.
-   **Searching file contents:** Select **Code** and use `repo:`,
    `path:`, `filename:`, or `extension:`.
-   **Private repository not visible:** Confirm that you are signed in
    with an account that has access.
-   **A qualifier does not work:** It may not be supported for that
    result type. Use the result-type tab and consult GitHub's current
    search documentation.

## Remember

**Build the search in layers:**

1.  Choose the result type (`is:pr`, `is:issue`, or the
    Code/Repositories/Users tab).
2.  Narrow to a repository (`repo:OWNER/REPO`) when appropriate.
3.  Choose where to search (`in:title`, `in:body`, `filename:`,
    `path:`).
4.  Add state, people, labels, dates, or counts.
5.  If results are empty, remove filters one at a time.
