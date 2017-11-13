# Migrations in DotKernel

- [Migrations in DotKernel](#migrations-in-dotkernel)
    - [Migrations](#migrations)
        - [What is a migration](#what-is-a-migration)
        - [How to use](#how-to-use)
    - [Seeds](#seeds)
        - [What are seeds](#what-are-seeds)
        - [How to use seeds](#how-to-use-seeds)
    - [Read more](#read-more)

## Migrations

DotKernel 3 has opted to use `migrations` instead of shipping the stack with a SQL dump. This makes it easier for developers to work in unison, and it's possible to deploy new SQL changes without having to touch a production database.

A migration registers the difference in the database, and carries it out when the `migrate` command is run. Every member can then make their desired changes to the database, and instead of sending memos around in Slack or IRC, they simply commit their migrations to their VCS of choice, and ask people run the `migrate` command.

Every migration describes an alteration in the database in some way, it can be a big change like an entirely new table, or a small change like changing `username` from `varchar(150)` to `varchar(200)`. However making this change in a migration makes sure that the same database will be present with every developer on the project, and every single production machine as well.

> This is especially brilliant, It'll run all *new* migrations. Which means you can go from any link in the chain, up to the newest version of the Schema

### What is a migration

```php
<?php
namespace Data\Database\Migrations;

use Phinx\Migration\AbstractMigration;

class VideoTable extends AbstractMigration
{
    protected $defaultTableSettings = ['signed' => false, 'collation' => 'utf8mb4_general_ci'];


    public function change()
    {
      $this->table('users', $this->defaultTableSettings)
            ->addColumn('username', 'string', ['limit' => 150])
            ->addColumn('email', 'string', ['limit' => 150])
            ->addColumn('password', 'string', ['limit' => 150])
            ->addColumn(
                'status',
                'enum',
                ['values' => ['pending', 'active', 'inactive', 'deleted'], 'default' => 'pending']
            )
            ->addColumn('dateCreated', 'timestamp', ['default' => 'CURRENT_TIMESTAMP', 'null' => true])
            ->addColumn('updated', 'timestamp', ['default' => null, 'null' => true])
            ->addIndex(['username', 'email'], ['unique' => true])
            ->create();
    }
}

```

In the above example, you see we use the `change` method, to alter the database. Everything inside that method will be "added" to the database when a migration is run, and it will be undone automatically if a migration is `rollback`.

As you may have noticed, this is a migration for creating the `users` table. It's a simple and readable approach creating, altering or deleting tables in a database.

### How to use

Make sure you have setup the `migrations.php` file found in the `config` directory, we recommend leaving the paths as is, but they can be customized for your project.

You can access the migration commands in DotKernel by running `php dot /vendor/bin/phinx --configuration=config/migrations.php {CommandHere}`

Our integration of migrations is based on  [Phinx](https://book.cakephp.org/3.0/en/phinx/migrations.html), and any command that is in Phinx, can be accessed through the command above.

> If you think the command is a bit of a mouthful to type every time, there is an unofficial package that makes it a bit easier, check out [JapSeyz/dot-migrations](https://github.com/japseyz/dot-migrations)

The command-line allows you to create and execute migrations as well as  seeds.

## Seeds

Seeds, or Seeders, work similarly to migrations, because they also alter the database, but instead of changing the schema, they alter the data within the database.

Seeds can be used to load a default user into the database every time the user-table migration is run.

> It's often used to either load a heap of test-data into the database, or load all the default data into the database when moving to production.

### What are seeds

```php
<?php
namespace Data\Database\Seeds;

use Phinx\Seed\AbstractSeed;

class UserTableSeeder extends AbstractSeed
{
    public function run()
    {
        $currentTime = new \DateTime();
        $currentTime = $currentTime->format('Y-m-d H:i:s');

        $table = $this->table('user');
        $table->insert([
            [
                'username' => 'Admin',
                'email' => 'admin@johnson.com',
                'password' => password_hash('Password', PASSWORD_DEFAULT),
                'status' => 'active',
                'dateCreated' => $currentTime,
                'updated' => $currentTime
            ]
        ])->save();

        $table = $this->table('user_details');
        $table->insert([
            [
                'userId' => 1,
                'firstName' => 'Admin',
                'lastName' => 'Johnson',
                'phone' => '00000000',
                'address' => '90210 Beverly Hills',
                'created' => $currentTime,
                'updated' => $currentTime
            ]
        ])->save();
    }
}
```

In the example above, we load a default user into the database, and add some data to the user in another table.

First, we select which table to work on, and after that, which data to enter into the table.
Next, we do the same, but on another table.

### How to use seeds

Seeds work just like migrations, and can be run via the `php dot /vendor/bin/phinx --configuration=config/migrations.php seed:run` command.
This command will run every seed that you have defined, but takes an optional `-s` flag, that allows you to specific the FQCN to a seeder, and only run that one.

## Read more

There's a Medium article, written by one of our developers, available [here](https://medium.com/@JapSeyz/database-migrations-and-why-you-should-use-them-11bfde52d7c2)
