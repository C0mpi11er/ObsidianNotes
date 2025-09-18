 Misconfiguration of the database sometimes results in verbose error messages. These can provide information that may be useful to an attacker. For example, consider the following error message, which occurs after injecting a single quote into an id parameter:
Unterminated string literal started at position 52 in SQL SELECT * FROM tracking WHERE id = '''. Expected char



to induce web apps to generate error message

You can use the `CAST()` function to achieve this. It enables you to convert one data type to another. For example, imagine a query containing the following statement:

`CAST((SELECT example_column FROM example_table) AS int)`

Often, the data that you're trying to read is a string. Attempting to convert this to an incompatible data type, such as an `int`, may cause an error similar to the following:

`ERROR: invalid input syntax for type integer: "Example data"`

This type of query may also be useful if a character limit prevents you from triggering conditional responses

# self note you need to get none errors as you proceed to know the paylaods that work here try with place holders and intergers before proceeding 

add LIMIT 1 incase  more than one data is produced

use as ref ' AND 1=CAST((SELECT password FROM users LIMIT 1)AS INT)--
