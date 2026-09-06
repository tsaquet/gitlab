# Gitlab discovery

## To Do

1. Create an account on gitlab.com
2. Account configuration (add ssh key)
3. Configure double auth
4. Create a project
5. Create an issue board & add issues (work items), test the labels
6. Commit & push (from your machine and from the web IDE)
7. Pull to get the changes back on your machine
8. Merge requests: one created from a branch, one created from an issue

## Solution 

1. Go to https://gitlab.com/users/sign_up & create an account (a free account is limited to 5 users per top-level group)
2. Go to https://gitlab.com/-/user_settings/ssh_keys & follow https://docs.gitlab.com/user/ssh/ (generate a key pair or reuse an existing one), then test with `ssh -T git@gitlab.com`
3. Go to https://gitlab.com/-/profile/two_factor_auth & click on "Register authenticator". Use any TOTP app (Aegis, FreeOTP, Google Authenticator...) & scan the QR code. Save the recovery codes.
4. Go back to https://gitlab.com/ & click on "New project" (Choose "Create blank project")
5. Go to "Plan" > "Issues" and start creating issues (they are "work items" in the new UI)
6. Go to "Plan" > "Issue boards" and start playing with boards, create a new one, add lists based on labels and see how moving a card changes the labels of the issue
7. Clone the project with the SSH URL, commit & push from your machine. Then edit a file from the "Web IDE" (Edit button) and commit from the browser. Run `git pull` on your machine to get that commit
8. Create a branch, push it, GitLab shows a "Create merge request" button. Then open an issue and click "Create merge request": GitLab creates the branch and the MR for you, both linked to the issue (merging closes it)
