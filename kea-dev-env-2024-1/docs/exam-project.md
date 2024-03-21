# Exam Project

Build a development environment for a software project of your choice that covers:

- Version Control System (Git on GitLab)
- Software Quality Assurance
- Containerization (Docker)
- Continuous Integration (GitLab CI)

The project must include at least:

- A web server (e.g., nginx)
- An application server
- A database server

These services must be part of your dockerized system, i.e., you can not rely on a cloud-hosted database system, etc.

The software project would ideally be a development project you are working on currently.
If that is not possible, you can use the Twitter clone from last semester, but make sure you make use of a database server rather than SQLite.


## Version Control System

Document your Git strategy.
Consider how your Git strategy aligns with continuous integration.


## Software Quality Assurance

Your development environment must be designed to improve software quality.
Implement the following:

- Testing
- Linting
- Security auditing of dependencies
- Code checking
- Code coverage


## Containerization

Containerize your project.

- You must be able to start your whole project with just one command
- It's a good idea (not must) if you can scale your application service


## Continuous Integration

Implement a continuous integration pipeline on GitLab.

- Static testing
- Runtime testing (requests through the web server involving the database)
- Build any custom Docker images
- Store Docker images in GitLab container registry


## Other Considerations

Make sure:

- Your decision process is clear and documented
- You evaluate your choices; compare to alternatives
- Reflect on your choices; what have we learned, would we do it different next time
- Discuss what value the development environment adds to your software project


## General Requirements

The project is subject to the requirements mentioned in the curriculum for your study program.

Please consult Wiseflow as per usual regarding hand-in dates, etc.

