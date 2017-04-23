# MusicalLadder TESTING
<h1>Musical Ladder</h1>

<h2>REQUIREMENTS</h2>
- VirtualBox
- Vagrant
- npm
- homestead
- heidisql or other mysql interface

<h2>Transision Tasks (not in production)</h2>
<hr>

<h3>Create Admin</h3>
__note__ You must add the 'school' admin.musicalladder.domain?.com to your hosts file
__note__ It does not matter what value you enter for password below, you will have to use Tinker to update the password

* add new school
    * `insert into schools (name, slug, created_at, updated_at, firstname, lastname, email, google_client_id, google_service_account_name, google_key_file_location, google_sheet_id) values ('Admin', 'admin', now(), now(), '','','','','','','')`
* add new role
    * `insert into roles (name, slug, description, level, created_at, updated_at) values ('Admin', 'admin', 'Super Administrator', 1, now(), now())`
* add new user
    * <i>insert into users
    (firstname, lastname, email, `password`, school_id, created_at, updated_at,
    address, address2, city, state, zip, cell_phone, work_phone, home_phone,
    date_enrolled, date_withdrawn, avatar, birthday, gender, health_concerns,
    image_waiver, responsible_party, import_id, skype, alternate_email)
    values
    ('<span style="color:red">firstname</span>','<span style="color:red">lastname</span>','<span style="color:red">email</span>',
    '$2y$10$Ydiz0DBoAY1AL2B8e2Yg5uYJkS3lVZ9LPAoz4F2MAlWRS7tGMg/fe',
    <span style="color:yellow">(select id from schools where slug='admin')</span>, now(), now(), '', '', '', '',
    '', '', '', '', now(), now(), '', '', '', '', '', '', '', '', '')</i>
    * The password hash is for 'secret'
* associate the user to the role
    * <i>insert into role_user (role_id, user_id, created_at, updated_at)
    values (<span style="color:yellow">(select id from roles where slug='admin')</span>, <span style="color:yellow">(select id from users where email='<span style="color:red">email</span>')</span>, now(), now())</i>
* update password with tinker:
    * php artisan tinker<br>
        namesapce App;<br>
        $user = User::where('email', 'marty@musicalladder.com')->first();<br>
        $user->password = \Hash::make('secret');<br>
        $user->save();<br>


<h3>Add roles to user table</h3>

* Add column `roles` in DB to `users` table
* Use tinker
    * $raw = \DB::table('users')->join('role_user', 'role_user.user_id', '=', 'users.id')->where('role_user.role_id', 1)->get();
        * 1: director
        * 2: teacher
        * 3: parent
        * 4: student
        * 5: admin
    *     foreach ($raw as $u):
            $user = User::find($u->user_id);
            if ($user):
              $user->role='director';
              $user->save();
            endif;
          endforeach;
* Do this for each type
* Or from mysql
    * update users set role = 'student' where id in (select user_id from role_user where role_id = 4)





