# Spooked!

##sql

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

##´¥·¢Æ÷
-- Updates a row in public.posts
create or replace function public.handle_updated_at() 
returns trigger as $$
begin
  update posts 
     set updated_at = new.created_at 
   where id = new.postid;
  return new;
end;
$$ language plpgsql security definer;

-- Trigger the function every time a user is created
create trigger on_new_reply
  after insert on public.replies
  for each row execute procedure public.handle_updated_at();

-- Inserts a row into public.account
create or replace function public.handle_new_user() 
returns trigger as $$
begin
  insert into public.account (id, email)
  values (new.id, new.email);

  return new;
end;
$$ language plpgsql security definer;

-- Trigger the function every time a user is created
create trigger on_auth_user_created
  after insert on auth.users
  for each row execute procedure public.handle_new_user();

## run
npm run dev -- --open
