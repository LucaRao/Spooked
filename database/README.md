# Database

The database and authentication is done entirely with Supabase.

📧 Email and password authentication is in place on Spooked!

There are four tables:

- public.account
- posts
- replies

The **account** table has three columns, it automatically filled by the trigger:

- id (uuid)
- joined_at
- email

The **posts** table has columns for:

- id
- created_at
- updated_at
- title
- body
- authorid  (FK to auth.users id)

The **replies** table has columns for:

- id
- created_at
- postid (FK to a post)
- authorid (FK to auth.users id)
- body


The **reports** table has columns for:
- id
- created_at
- created_by  
- reported_post 
- reported_user  
- description



 CREATE TABLE "public"."account" ( 
  "id" UUID NOT NULL,
  "joined_at" TIMESTAMP WITH TIME ZONE NULL,
  "email" TEXT NOT NULL,
  CONSTRAINT "account_pkey" PRIMARY KEY ("id")
);

CREATE TABLE "public"."posts" ( 
  "id" SERIAL,
  "created_at" TIMESTAMP WITH TIME ZONE NULL,
  "updated_at" TIMESTAMP WITH TIME ZONE NULL,
  "title" TEXT NOT NULL,
  "body" TEXT NOT NULL,
  "authorid" UUID NOT NULL,
  CONSTRAINT "posts_pkey" PRIMARY KEY ("id"),
  CONSTRAINT "posts_authorid_fkey" FOREIGN KEY ("authorid") REFERENCES "auth"."users" ("id") ON DELETE NO ACTION ON UPDATE NO ACTION
);


CREATE TABLE "public"."replies" ( 
  "id" SERIAL,
  "created_at" TIMESTAMP WITH TIME ZONE NULL,
  "postid" BIGINT NOT NULL,
  "authorid" UUID NOT NULL,
  "body" TEXT NOT NULL,
  CONSTRAINT "replies_pkey" PRIMARY KEY ("id"),
  CONSTRAINT "replies_authorid_fkey" FOREIGN KEY ("authorid") REFERENCES "auth"."users" ("id") ON DELETE NO ACTION ON UPDATE NO ACTION,
  CONSTRAINT "replies_postid_fkey" FOREIGN KEY ("postid") REFERENCES "public"."posts" ("id") ON DELETE NO ACTION ON UPDATE NO ACTION
);


CREATE TABLE "public"."reports" ( 
  "id" SERIAL,
  "created_at" TIMESTAMP WITH TIME ZONE NULL,
  "created_by" UUID NOT NULL,
  "reported_post" BIGINT NOT NULL,
  "reported_user" UUID NOT NULL,
  "description" TEXT NOT NULL,
  CONSTRAINT "reports_pkey" PRIMARY KEY ("id")
);