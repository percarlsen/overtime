# Overtime

A simple program to register overtime work hours.

## Dependencies
- Postgres (tested on 14.9)
- Bash >= 4.2 


## Installation
1. Clone this repo.
2. Change directory to the repo and make the program executable with

```bash
chmod +x overtime
```

4. (Optional) add to path to access it anywhere

```bash
export PATH=$PATH:`pwd`
```

5. Run the initial installation. This step will create a postgres database named `overtime` (you may specify another name with the `-c` flag, in which case this has to be provided in all commands) with one table called `overtime`. This is where all logged hours will be stored.

```bash
overtime install
```

6. You're good to go. A good starting point is probably

```bash
overtime -h
```

## Examples

Add two and a half hour overtime for today with a custom message:

```bash
overtime add -m "Worked a lot today" 2 30
# or, if you used a custom db name (this goes for all examples below as well):
overtime -c my_custom_db_name add -m "Worked a lot today" 2 30
```

Add one hour on another day:

```bash
overtime add -d "2022-02-02" 1
```

Add 30 minutes of negative hours. There are three ways to achieve this. Either use the `flex` subcommand, the `add` subcommand with the `-n` flag or the `add` subcommand with negative numbers:

```bash
overtime flex 0 30
# or:
overtime add -n 0 30
# or:
overtime add 0 -30
```

See the log

```bash
overtime log
 id |         created_at         |    date    | hours | minutes |      message
----+----------------------------+------------+-------+---------+--------------------
 28 | 2022-04-06 00:16:56.309263 | 2022-04-06 |     0 |     -30 |
 27 | 2022-04-06 00:16:48.850319 | 2022-02-02 |     1 |       0 |
 26 | 2022-04-06 00:16:42.484353 | 2022-04-06 |     2 |      30 | Worked a lot today
```

Check your current balance

```bash
overtime balance
 hours_balance | minutes_balance
---------------+-----------------
             3 |               0
```

Undo the last entry

```bash
overtime undo
overtime log
 id |         created_at         |    date    | hours | minutes |      message
----+----------------------------+------------+-------+---------+--------------------
 27 | 2022-04-06 00:16:48.850319 | 2022-02-02 |     1 |       0 |
 26 | 2022-04-06 00:16:42.484353 | 2022-04-06 |     2 |      30 | Worked a lot today
```

You can of course also work with the data directly from postgres:

```bash
psql -d overtime  # substitute "overtime" with whatever custom db name you may have chosen during installation

overtime=# \d
              List of relations
 Schema |      Name       |   Type   | Owner
--------+-----------------+----------+-------
 public | overtime        | table    | per
 public | overtime_id_seq | sequence | per

overtime=# select * from overtime;
 id |         created_at         |    date    | hours | minutes |      message
----+----------------------------+------------+-------+---------+--------------------
 26 | 2022-04-06 00:16:42.484353 | 2022-04-06 |     2 |      30 | Worked a lot today
 27 | 2022-04-06 00:16:48.850319 | 2022-02-02 |     1 |       0 |
```

## TODOs

- The program has only been tested on macOS Monterey. Date functions used by overtime differ on Linux, so it won't necessarily work out-of-the-box.
