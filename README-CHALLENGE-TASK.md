# Challenge Task - Weekly Challenge Tracker

One challenge per week (Sunday to Saturday). Track progress with check-ins, mark complete, and review history.

## Setup

This app uses the **same Supabase project** as your other apps. Just create these new tables:

**In Supabase SQL Editor, run:**

```sql
create table public.weekly_tasks (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references auth.users(id) on delete cascade,
  week_start date not null,
  task_description text not null,
  completed_at timestamptz,
  created_at timestamptz default now()
);

create unique index weekly_tasks_user_week_idx on public.weekly_tasks (user_id, week_start);

create table public.task_check_ins (
  id uuid primary key default gen_random_uuid(),
  task_id uuid not null references public.weekly_tasks(id) on delete cascade,
  user_id uuid not null references auth.users(id) on delete cascade,
  checked_in_at timestamptz not null default now()
);

create index task_check_ins_task_idx on public.task_check_ins (task_id);

alter table public.weekly_tasks enable row level security;
alter table public.task_check_ins enable row level security;

create policy "Read own tasks" on public.weekly_tasks for select to authenticated using ((select auth.uid()) = user_id);
create policy "Create own tasks" on public.weekly_tasks for insert to authenticated with check ((select auth.uid()) = user_id);
create policy "Update own tasks" on public.weekly_tasks for update to authenticated using ((select auth.uid()) = user_id);

create policy "Read own check-ins" on public.task_check_ins for select to authenticated using ((select auth.uid()) = user_id);
create policy "Create own check-ins" on public.task_check_ins for insert to authenticated with check ((select auth.uid()) = user_id);
create policy "Delete own check-ins" on public.task_check_ins for delete to authenticated using ((select auth.uid()) = user_id);

grant select, insert, update on public.weekly_tasks to authenticated;
grant select, insert, delete on public.task_check_ins to authenticated;
```

That's it! The app already has your Supabase credentials and will work alongside your other apps.
