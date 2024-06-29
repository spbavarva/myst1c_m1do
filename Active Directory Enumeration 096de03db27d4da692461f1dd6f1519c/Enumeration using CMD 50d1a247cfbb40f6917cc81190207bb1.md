# Enumeration using CMD

### Using Net.exe

```markdown
**Query Domain Users Information:**

net user /domain

*Find out if a particular user has administrator group priveleges:*

net user jeffadmin /domain

*enumerating user groups:*

net group /domain

*Enumerating members of a particular group:*

net group "Sales Department" /domain
```