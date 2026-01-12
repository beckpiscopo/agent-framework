# Smart Commit

Create a well-formatted git commit following conventional commits.

## Steps

1. Run `git status` to see all changes
2. Run `git diff --staged` to review what will be committed
3. If nothing is staged, ask if I should stage all changes or specific files
4. Analyze the changes and determine the commit type:
   - `feat`: New feature
   - `fix`: Bug fix
   - `docs`: Documentation only
   - `style`: Formatting, no code change
   - `refactor`: Code change that neither fixes a bug nor adds a feature
   - `test`: Adding or updating tests
   - `chore`: Maintenance tasks
5. Generate a commit message in this format:
   ```
   type(scope): short description

   - Bullet point details if needed
   - Keep it concise
   ```
6. Show me the proposed commit message and ask for confirmation
7. If confirmed, run the commit
8. Show the result

## Example Output

```
Proposed commit:

feat(auth): add password reset flow

- Add forgot password endpoint
- Create reset token generation
- Add email sending utility

Proceed with this commit? (y/n)
```
