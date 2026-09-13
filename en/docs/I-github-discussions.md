# GitHub Discussions Introduction

## What is GitHub Discussions?

GitHub Discussions is a community-oriented Q&A and discussion platform, similar to forums. It allows teams, communities and users to discuss around projects.

## Discussions vs Issues

| Feature | Discussions | Issues |
|---------|-------------|--------|
| Purpose | Q&A, discussion, sharing | Track bugs, feature requests |
| Structure | Categories, labels, answers | Labels, milestones, assignees |
| Answers | Can mark best answer | No answer concept |
| Social | Likes, comments, follows | Comments, subscriptions |
| Closing | No need to close | Can be closed |

## Enable Discussions

1. Go to repository **Settings**
2. Check **Discussions** in **Features** section
3. Click **Set up discussions**

## Category Settings

### Default Categories

| Category | Purpose |
|----------|---------|
| 📣 Announcements | Official announcements |
| 💬 General | General discussion |
| 💡 Ideas | Ideas and suggestions |
| 🙋 Q&A | Questions and answers |
| 📝 Show and tell | Showcase and sharing |

### Custom Categories

1. Go to **Discussions** page
2. Click **Categories**
3. Click **New category**
4. Set name, description and icon

## Usage Scenarios

### 1. Project Q&A

```
Title: How to configure database connection?

I'm getting an error when trying to connect to PostgreSQL database...
[Error message]

Does anyone know how to solve this?
```

### 2. Feature Discussion

```
Title: Suggestion: Add dark mode support

I think this project could add dark mode functionality...
[Detailed description]

What does everyone think?
```

### 3. Community Showcase

```
Title: I built a XXX with this project

Hello everyone, I developed an application using this project...
[Screenshots and description]

Welcome feedback!
```

## Best Answer

1. Reply in discussion
2. Click **...** menu next to reply
3. Select **Mark as answer**

Best answer will be highlighted, making it easy for later users to quickly find solutions.

## Manage Discussion

### Lock Discussion
Prevent further comments:
1. Click **Lock discussion**
2. Select lock reason

### Convert to Issue
If discussion reveals a bug:
1. Click **Convert to issue**
2. Issue will link to original discussion

### Delete Discussion
1. Click **Delete discussion**
2. Confirm deletion

## Team Collaboration

### Code Owners

Define in `.github/CODEOWNERS`:
```
# Discussions managed by community team
.github/discussions/ @community-team
```

### Assign Responsible Person

Add responsible person in Discussion:
1. Click **Assignees** on right side
2. Select responsible person

## Best Practices

1. **Set Clear Categories**: Help users find correct discussion area
2. **Reply Promptly**: Keep community active
3. **Mark Best Answers**: Convenient for later users
4. **Regular Cleanup**: Delete meaningless content
5. **Encourage Participation**: Thank contributors

## Integrate Other Features

### Associate Issue

```
Related Issue: #123
```

### Associate PR

```
Related PR: #456
```

### Add Labels

Use Issue labels to categorize discussions.

## Related Resources

- [GitHub Discussions Official Documentation](https://docs.github.com/en/discussions)
- [Community Management with Discussions](https://docs.github.com/en/discussions/collaborating-with-your-community-using-discussions)