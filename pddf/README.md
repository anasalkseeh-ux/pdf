# Setup (one time, free, about 10 minutes)

Two files: `index.html` (users) and `admin.html` (you). GitHub Pages cannot store data, so both use a free Supabase database. Check Supabase and GitHub are available in your country first. If not, tell me and I will switch the backend.

1. Create a free project at supabase.com.
2. SQL Editor → run this:

```sql
create table payments(
  id uuid primary key default gen_random_uuid(),
  name text, phone text, method text, ref text,
  status text default 'pending',
  created_at timestamptz default now());
alter table payments enable row level security;
create policy "admin only" on payments for all to authenticated using (true) with check (true);

create function submit_payment(p_name text,p_phone text,p_method text,p_ref text)
returns uuid language sql security definer as
$$ insert into payments(name,phone,method,ref) values(p_name,p_phone,p_method,p_ref) returning id $$;

create function check_payment(pid uuid)
returns text language sql security definer as
$$ select status from payments where id=pid $$;

grant execute on function submit_payment(text,text,text,text), check_payment(uuid) to anon;
```

3. Authentication → Providers → Email: turn OFF "Allow new users to sign up". Then Authentication → Users → Add user (your email and a strong password). This is your admin login.
4. Project Settings → API: copy the Project URL and the `anon public` key. Paste both at the top of `index.html` and `admin.html`.
5. In `index.html`, replace the placeholder account numbers in `METHODS` with your real Sham Cash and Syriatel Cash details. To add a method later, add one line.
6. Upload both files to a GitHub repo → Settings → Pages → deploy from `main`.
   Users open `.../index.html`. You open `.../admin.html`. Do not share the admin link.

## Limits to know
- Access is remembered per device. A user on a new phone must request again, or you can add a code system later.
- Photo to PDF works fully offline in the browser. Reading text from photos (OCR) is the next feature to add.
- Put your real terms and privacy text in the `TERMS` block in `index.html`.
