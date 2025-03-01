# Next.js Email Client

This is a simple email client built with Next.js and Postgres.

- Navigate between routes in a column layout while maintaining scroll position (layouts support)
- Submit forms without JavaScript enabled (progressive enhancement)
- Navigate between routes extremely fast (prefetching and caching)
- Retain your UI position on reload (URL state)

The first version of the UI was built with [v0](https://v0.dev/).

<img width="1343" alt="280543133-1e33ad53-832f-414563sd" src="https://github.com/dr-dark-flames/nextjs-email-client/assets/12224035/a62313d4-35fb-40b7-a73b-ad8844fa8a2b">

## Tech

- [Next.js](https://nextjs.org/)
- [Postgres](https://vercel.com/docs/storage/vercel-postgres)
- [Tailwind CSS](https://tailwindcss.com/)
- [TypeScript](https://www.typescriptlang.org/)
- [React Aria Components](https://react-spectrum.adobe.com/react-aria/index.html)

## Known Issues

- [ ] Forward / reply / aren't hooked up yet
- [ ] Need to add a way to manage folders
- [ ] Need to add a way to manage users
- [ ] Error handling for form submissions
- [x] Fix to/from to pull sender/recipient everywhere
- [x] Add search

## Setup

In order to run this project locally, you'll need to create a Postgres database and add the connection string to your `.env.local` file.

Further, you'll need to create the tables and insert some sample data.

### Steps to get started:

1. Create a Postgres database
2. Navigate to the `.env.local` tab in the quickstart section Postgres dashboard
3. Copy the snippet and paste it into your `.env.local` file
4. Run `pnpm run setup` to create the tables and insert sample data

This example uses `postgres`, which makes it compatible with any Postgres database. You can use `DATABASE_URL` or `POSTGRES_URL` to connect to your database.

```
POSTGRES_URL="postgres://user:password@host:port/database"
```

## Schema

```
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(255) UNIQUE NOT NULL
);

CREATE TABLE emails (
    id SERIAL PRIMARY KEY,
    sender_id INTEGER REFERENCES users(id),
    recipient_id INTEGER REFERENCES users(id),
    subject VARCHAR(255),
    body TEXT,
    sent_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE folders (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL
);

CREATE TABLE user_folders (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    folder_id INTEGER REFERENCES folders(id)
);

CREATE TABLE email_folders (
    id SERIAL PRIMARY KEY,
    email_id INTEGER REFERENCES emails(id),
    folder_id INTEGER REFERENCES folders(id)
);
```

## Sample Data

```
INSERT INTO users (first_name, last_name, email)
VALUES ('John', 'Doe', 'john.doe@example.com'),
       ('Jane', 'Doe', 'jane.doe@example.com'),
       ('Alice', 'Smith', 'alice.smith@example.com'),
       ('Bob', 'Johnson', 'bob.johnson@example.com');

INSERT INTO emails (sender_id, recipient_id, subject, body, sent_date)
VALUES (1, 2, 'Meeting Reminder', 'Don''t forget about our meeting tomorrow at 10am.', '2022-01-10 09:00:00'),
       (1, 3, 'Hello', 'Just wanted to say hello.', '2022-01-09 08:00:00'),
       (2, 1, 'Re: Meeting Reminder', 'I won''t be able to make it.', '2022-01-10 10:00:00'),
       (3, 1, 'Re: Hello', 'Hello to you too!', '2022-01-09 09:00:00'),
       (4, 1, 'Invitation', 'You are invited to my party.', '2022-01-11 07:00:00'),
       (1, 2, 'Work Project', 'Let''s discuss the new work project.', '2022-01-12 07:00:00'),
       (1, 4, 'Expenses Report', 'Please find the expenses report attached.', '2022-01-13 07:00:00'),
       (4, 1, 'Personal Note', 'Let''s catch up sometime.', '2022-01-14 07:00:00');

INSERT INTO folders (name)
VALUES ('Inbox'),
       ('Flagged'),
       ('Sent'),
       ('Work'),
       ('Expenses'),
       ('Personal');

INSERT INTO user_folders (user_id, folder_id)
VALUES (1, 1), (1, 2), (1, 3), (1, 4), (1, 5), (1, 6),
       (2, 1), (2, 2), (2, 3), (2, 4), (2, 5), (2, 6),
       (3, 1), (3, 2), (3, 3), (3, 4), (3, 5), (3, 6),
       (4, 1), (4, 2), (4, 3), (4, 4), (4, 5), (4, 6);

INSERT INTO email_folders (email_id, folder_id)
VALUES (1, 1),
       (2, 1),
       (3, 3),
       (4, 1),
       (5, 1),
       (6, 4),
       (7, 5),
       (8, 6);
```

## Database Relationships

- Users can send and receive emails (users.id -> emails.sender_id and emails.recipient_id)
- Users can have multiple folders (users.id -> user_folders.user_id)
- Folders can contain multiple emails (folders.id -> email_folders.folder_id)
- An email can be in multiple folders (emails.id -> email_folders.email_id)
