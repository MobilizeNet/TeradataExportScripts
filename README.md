
# Teradata DDL Export Sample Script

Sometimes you need to generate DDL scripts for all the objects in your
teradata databases.

These scripts are provided as a reference to perform that task.

# How to use these scripts

## To Extract Teradata DDL Code

Download or clone the repository into your server. Lets say under a folder called `extract`

Once you are in that folder:

- Create an output folder
`mkdir out`

- add your settings in the `create_ddl_config.sh` in that file you can specify which database(s) to export and which elements to include

- run the `create_ddls.sh` script
For example for host: `localhost` user: `dbc` and password: `dbc` and output folder: `out` use this:

```shell
./create_ddls.sh localhost dbc dbc ./out
```

After execution go to the output folder. All your scripted objects will be in DDL_xxx.sql files

You can look at a sample extraction in the **SampleOutput** folder in this repo.

## To handle embedded BTEQ Code

It is very common to encounter scenarios where you have embedded BTEQ inside your shell scripts.
For example something like this:

```bash
echo 'unix command'
echo 'unix command'
bteq << EOF
.logon <systemid>/<userid>,<password>;
select current_timestamp;
.logoff;
.quit;
EOF
echo 'unix command 3'
echo 'unix command 4'
echo 'unix command 5'
```

In those scenarios you can use these helpers scripts. You can run them like this:

```
python extract_bteq_snippets.py shell_script_with_embedded_bteq.sh
```

This script will generate several files like:
* shell_script_with_embedded_bteq.sh.pre.sh
* shell_script_with_embedded_bteq.sh.snippet.1.bteq

You can then feed those bteq files to the migration tool.

After migration just run

```
python restore_bteq_snippets.py shell_script_with_embedded_bteq.sh.pre.sh
```

And it will rebuild your original file replacing your 
```bash
bteq << EOF
.REMARK bteq code
EOF
```

fragments by 
```bash
python <<END_SNOWSCRIPT
print("bteq code")
END_SNOWSCRIPT
```
