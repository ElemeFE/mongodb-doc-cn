# Configure the`mongo`Shell

On this page

* [Customize the Prompt](https://docs.mongodb.com/manual/tutorial/configure-mongo-shell/#customize-the-prompt)
* [Use an External Editor in the`mongo`Shell](https://docs.mongodb.com/manual/tutorial/configure-mongo-shell/#use-an-external-editor-in-the-mongo-shell)
* [Change the`mongo`Shell Batch Size](https://docs.mongodb.com/manual/tutorial/configure-mongo-shell/#change-the-mongo-shell-batch-size)

## Customize the Prompt

You may modify the content of the prompt by setting the variable`prompt`in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell. The`prompt`variable can hold strings as well as JavaScript code. If`prompt`holds a function that returns a string,[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)can display dynamic information in each prompt.

You can add the logic for the prompt in the[.mongorc.js](https://docs.mongodb.com/manual/reference/program/mongo/#mongo-mongorc-file)file to set the prompt each time you start up the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell.

### Customize Prompt to Display Number of Operations

For example,to create a[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell prompt with the number of operations issued in the current session, define the following variables in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell:

```
cmdCount
=
1
;
prompt
=
function
()
{
return
(
cmdCount
++
)
+
"
>
 "
;
}
```

The prompt would then resemble the following:

```
1
>
2
>
3
>
```

### Customize Prompt to Display Database and Hostname

To create a[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell prompt in the form of`<database>@<hostname>$`, define the following variables:

```
host
=
db
.
serverStatus
().
host
;
prompt
=
function
()
{
return
db
+
"@"
+
host
+
"$ "
;
}
```

The prompt would then resemble the following:

```
test@myHost1$

```

### Customize Prompt to Display Up Time and Document Count

To create a[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell prompt that contains the system up time_and_the number of documents in the current database, define the following`prompt`variable in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell:

```
prompt
=
function
()
{
return
"Uptime:"
+
db
.
serverStatus
().
uptime
+
" Documents:"
+
db
.
stats
().
objects
+
" 
>
 "
;
}
```

The prompt would then resemble the following:

```
Uptime
:
5897
Documents
:
6
>
```

## Use an External Editor in the`mongo`Shell

You can use your own editor in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell by setting the[`EDITOR`](https://docs.mongodb.com/manual/reference/program/mongo/#envvar-EDITOR)environment variable_before_starting the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell.

```
export
EDITOR
=
vim
mongo

```

Once in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell, you can edit with the specified editor by typing`edit<variable>`or`edit<function>`, as in the following example:

1. Define a function`myFunction`:

   ```
   function
   myFunction
   ()
   {
   }
   ```

2. Edit the function using your editor:

   ```
   edit
   myFunction
   ```

   The command should open the`vim`edit session. When finished with the edits, save and exit`vim`edit session.

3. In the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell, type`myFunction`to see the function definition:

   ```
   myFunction
   ```

   The result should be the changes from your saved edit:

   ```
   function
   myFunction
   ()
   {
   print
   (
   "This was edited"
   );
   }
   ```

NOTE

As[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell interprets code edited in an external editor, it may modify code in functions, depending on the JavaScript compiler. For[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)may convert`1+1`to`2`or remove comments. The actual changes affect only the appearance of the code and will vary based on the version of JavaScript used but will not affect the semantics of the code.

## Change the`mongo`Shell Batch Size

The[`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)method is the JavaScript method to retrieve documents from a[collection](https://docs.mongodb.com/manual/reference/glossary/#term-collection). The[`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)method returns a[cursor](https://docs.mongodb.com/manual/reference/glossary/#term-cursor)to the results; however, in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell, if the returned cursor is not assigned to a variable using the`var`keyword, then the cursor is automatically iterated up to 20 times to print up to the first 20 documents that match the query. The[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell will prompt`Typeit`to iterate another 20 times.

You can set the`DBQuery.shellBatchSize`attribute to change the number of documents from the default value of`20`, as in the following example which sets it to`10`:

```
DBQuery
.
shellBatchSize
=
10
;
```



