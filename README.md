This style guide is based on [PEP8](https://www.python.org/dev/peps/pep-0008) with references to:

* [Google Python Style Guide](https://github.com/google/styleguide/blob/gh-pages/pyguide.md)
* [EdX Python style guide](https://edx.readthedocs.io/projects/edx-developer-guide/en/latest/style_guides/python-guidelines.html)
* [Django coding style](https://docs.djangoproject.com/en/1.11/internals/contributing/writing-code/coding-style)

`PEP 8` is a style guide for `Python` code that is widely accepted and used, and is at the core of this guide. This,
along with the remaining references found at the start of this document, should be taken into account for any missing
guideline in the steps described below.

**Content:**

* [Imports order](#imports-order)
* [Constants variables](#constants-variables)
* [Code lines length](#code-lines-length)
* [Breaking long lines](#breaking-long-lines)
* [Keyword arguments](#keyword-arguments)
* [Named arguments](#named-arguments)
* [Class names](#class-names)
* [Trailing commas](#trailing-commas)
* [Unused arguments](#unused-arguments)
* [Bare exceptions](#bare-exceptions)
* [Types declaration](#types-declaration)
* [Comprehensions](#comprehensions)
* [Typing](#typing)
  * [Variable typing](#variable-typing)
* [Is instance](#is-instance)
* [Functional programming](#functional-programming)
* [Mutable objects as function arguments](#mutable-objects-as-function-arguments)
* [One line comments](#one-line-comments)
* [Strings](#strings)
* [Code documentation](#code-documentation)
  * [Patterns](#patterns)
    * [Module](#module)
    * [Class](#class)
    * [Function](#function)
* [Tests](#tests)
  * [Documentation](#documentation)
  * [Asserts](#asserts)
* [Indentation](#indentation)
* [Not changeable summary lines](#not-changeable-summary-lines)
* [Paths](#paths)
* [References](#references)

## Imports order

The common imports order is recommended, which means this order:

1. Standard library imports.
2. Third party library imports.
3. Local imports.

There are rules:

* Alphabetic order inside each group of import.
* Use a single blank line to separate groups.
* Inside each group locate `import` above `from ... import ...`.
* If you import more than one entity from one place, move each to own line.

```python
import os
from sys import argv

import requests
from flask import Flask

from application.models.errors (
    UserAlreadyExists,
    UserDoesNotExists,
)
from application.models.user import User
from application.settings.database import DSN_URL
```

## Constants variables

There are no reasons to use bare values in code.

```python
if car.mileage < 5000:
    raise ...
```

Code reader won't understand what the mileage variable related to and why this particular integer is specified. Great way is to explicity say the intention of the code piece.

```python
if mileage < REQUIRED_CARS_TECHNICAL_INSPECTION_MILEAGE:
    raise ...
```

## Code lines length

Use 120 characters as a limit for code line always you can.

```python

arguments, errors = CreateUserForm(user=user)

if errors:
    return json_response(error=errors, status_code=HttpStatus.OK, message=CreateUserForm.full_message)
```

Except long line which contains a bit complex logic that code reader should pays attention on.

```python
ethereum_transaction = EthereumTransaction.create(
    inputs=inputs,
    outputs=outputs,
    header=header,
    signature=signature,
    public_key=public_key,
)
```

## Breaking long lines

If you need to break a function call over more than one line, put a newline after the open paren, and move the arguments to their own line. Do not indent everything to where the open paren is. This makes the code too indented, and makes different function calls near each other indented different amounts.

```python
user = create_user(
    name='Elon', surname='Musk', age=47, company='SpaceX', citizenship='United States',
)
```

Or:

```python
user = create_user(
    name='Elon',
    surname='Musk',
    age=47,
    company='SpaceX',
    citizenship='United States',
)
```

But never:

```python
user = create_user(name='Elon', surname='Musk',
                   age=47, company='SpaceX',
                   citizenship='United States')
```

So, closing paren should be on a line by itself, indented the same as the first line and the first line ends with the open paren.

## Keyword arguments

Keyword argument is essentially different than a variable assignment, so instead:

```python
user = create_user(name = 'Elon', surname = 'Musk')
```

Use:

```python
user = create_user(name='Elon', surname='Musk')
```

## Named arguments

Whenever possible pass arguments with specified name:

```python
user = create_user(name='Elon', surname='Musk')
```

Naked values aren't allowed:

```python
user = create_user('Elon', 'Musk')
```

The exception to this is when the corresponding parameter is
_positional-only_.

### Positional-only parameters

#### Built-in functions

Some built-in functions have positional-only arguments.
This is, for example, the case of the function `len` or the `list` method `append`.

#### Extension modules

Some functions defined in modules implemented in `C` or `C++` also have positional-only arguments.

#### Parameters defined as positional-only

Python 3.8 introduces syntax to _positional-only_ arguments.

_Positional-only_ parameters can be defined by including a `/` character in the parameter list
of the function definition after them, for example _positional_only_1_ and _positional_only_2_ in the following:

```python
def predict(positional_only_1, positional_only_2, /, positional_or_keyword):
    ...
```

## Class names

Class names should start from capital letter. Acronyms should count as one word.

```python
class HtmlTreeParser:
    ...
```

## Trailing commas

Trailing commas prevent editing the last line when adding a new last line. Use them in lists, dicts, function calls, etc.

```python
user = create_user(
    name='Elon',
    surname='Musk',
)
```

## Unused arguments

For unused arguments, use an underscore.

```python
_, errors = CreateUserForm(user=user)

if errors:
    ...
```

## Bare exceptions

Do not use bare exceptions:

```python
try:
    user = create_user(name='Elon', surname='Musk')

except Exception:
    pass
```

Catch particular exceptions instead:

```python
try:
    user = create_user(name='Elon', surname='Musk')

except UserDoesNotExists:
    pass
```

But if some library raise bare exception (could be sometimes), leave the note what was the reason of error.

## Types declaration

When declaring container, prefer:

```
users = {
    'deleted': first_user,
}
```

Over:

```python
users = dict(deleted=first_user)
```

It is a traditional way to declare types variables which never confuses another programmers.

## Comprehensions

You can use comprehensions (list, dict, etc.) as long as they are not more complex than. The same rule works for generator
expressions as well.

```python
_ = [function(variable1) for variable1 in variable2 if predictate(variable1)]
```

If your conditions or loops should be more complex, use regular for loops and if else statements.

## Typing

* Use `Python typing` features everywhere.

    ```python
    def create_user(name: str, surname: str, age: int) -> User:
        ...
    ```

* Declare types using explicit parameters, specifying the types for nested structures until you reach an individual object type.

    That is, instead of using generic ones:

    ```python
    def predict(data: dict) -> list:
        ...
    ```

    You should use `typing` from the `Python` standard library:

    ```python
    from typing import (
        Dict,
        List,
    )


    def predict(data: Dict[str, str]) -> List[Dict[str, float]]:
        ...
    ```

* Specify union types when more than one type is possible, for example as a value in a dictionary.

    ```python
    from typing import (
        Dict,
        Union,
    )


    def preprocess(data: str) -> Dict[str, Union[int, str]]:
        ...
    ```

* Use optional typing for optional arguments in order to be able to follow the rules regarding
[Mutable objects as function arguments](#mutable-objects-as-function-arguments):

    ```python
    from typing import Optional


    class PdfGenerationError(Exception):
        """
        PDF generation error exception implementation.
        """

        message_default = 'There was an error on generating the PDF'

        def __init__(self, message: Optional[str] = None) -> None:
            ...
    ```

### Variable typing

This rule is not checked by any linter configuration at the moment.

Adding typing when you declare variables means declaring a variable like so:

```python
from typing import List

learning_rate: List[float] = [0.1, 0.2, 0.4, 0.5, 0.6]
```

Instead of the plain version:

```python
learning_rate = [0.1, 0.2, 0.4, 0.5, 0.6]
```

`mypy` allows you to declare more than a single variable at a time, but only with a type comment.
We do not use type comments, so you can do one of two things:

1. In simple cases like this code snippet:

    ```python
    training_samples_min, training_samples_max = min(samples), max(samples)
    ```

    You can easily separate it into two lines:

    ```python
    training_samples_min: int = min(samples)
    training_samples_max: int = max(samples)
    ```

2. In more complex cases in which you retrieve different values from a function, for example, exceptions can be made.

    ```python
    training_samples_min, training_samples_max = get_samples_min_and_max(samples=samples)
    ```

## Is instance

If you want to check if instance is an instance of particular type, use:

```python
number = 5

if isinstance(number, int):
   ...
```

And not `type(number)` with any other checking.

## Functional programming

Just don't use `map()`, `filter()`, `reduce()`.

## Mutable objects as function arguments

Do not use mutable objects as function arguments:

```python
def create_user(permissions=[]):
    ...
```

Prefer immutable once such as `None`, `False`, etc.

References:

* https://learnandlearn.com/python-programming/python-how-to/python-function-arguments-mutable-and-immutable
* https://medium.com/@tyastropheus/tricky-python-ii-parameter-passing-for-mutable-immutable-objects-10e968cbda35

## One line comments

One line comments should be avoided. All information should be in the documentation of the function, class, or module.
Situations when you want to describe weird behaviour that you cannot explain in documentation, is an exception.
For instance, if your function is part of business logic (e.g. online payments), but you want to explain some technical
trick (e.g. `Python mutable objects` behaviour) that cannot be explained directly in the docstring.

## Strings

Use single quotes for most cases.

```python
message = 'User has been created.'
```

For exceptions, use double quotes, to avoid escaping with backslashes:

```python
message = "User's information does not fit request's incoming data."
```

It's fine if you have something similar to:

```
errors = {
    'estate': "Estate's identifier is required"
}
```

Use of `f-strings` is preferred:

```python
name = 'Den'

string = f'Hi, {name}!'
```

## Code documentation

1. Write documentation strings for each module, class and function.
2. Always format docstrings using the multi-line convention, even if there’s only one line of content.
3. Use three double-quotes for all docstrings.
4. Start with a one-line summary. If you can’t fit a summary onto one line, think harder, or refactor the code.
5. Put backticks around code names: variables, parameters, methods, etc.
6. Specify arguments which function accepts, errors function raises and what will function return.
7. If you function implements some algorithm, formula or another not-trivial specifications, leave references.

```python
"""
Provide implementation of database user creation.
"""


class CreateUser:
    """
    Database user creation implementation.
    """

    def by_nickname(nickname):
        """
        Create an user with specified nickname.

        Nickname should pass `NicknameCreationForm` class to ensure it contains only valid characters.

        Arguments:
            nickname (str): user nickname no longer 64 characters.

        Raises:
            UserAlreadyExists: when user with the same nickname already presented in database.

        References:
            - https://developers.google.com/admin-sdk/directory/v1/guides/manage-users

        Returns:
            Created user instance as `database.models.User`.
        """
        ...
```

### Patterns

#### Module

Pattern for module is:

```text
"""
Provide {what} for (of) {entity} {sub-entity}.
"""
```

Examples are:

```text
"""
Provide implementation of database user creation.
"""
```

```text
"""
Provide tests for database user creation.
"""
```

```text
"""
Provide utils for model data frame processing.
"""
```

#### Class

Patterns for class is:

```text
"""
{entity} {sub-entity} implementation.
"""
```

Examples are:

```text
"""
Database user creation implementation.
"""
```

```text
"""
Blockchain public key implementation.
"""
```

#### Function

Pattern for function is:

```text
"""
{Description in imperative, capital letter, full stop}.

{Detailed description}.

Arguments:
    {parameter} ({type}): {description, lower case, full stop}.

Raises:
    {error class}: {description, lower case, full stop}.

References:
    - {link}
    - {link}

Returns:
    {Description of returning values, type of the value is mentioned, capital letter, full stop}.
```

Example is:

```text
"""
Connect to the next available port.

Arguments:
    minimum (int): a port value greater or equal to 1024.

Raises:
    ConnectionError: if no available port is found.

References:
    - https://www.maketecheasier.com/check-open-ports-linux

Returns:
    The new minimum port as integer.
"""
```

The function docstring has to follow the rules described below:

* Don't specify nested types in arguments documentation, use the root type.

    ```
    Arguments:
        usernames (list): list of usernames.
    ```

* Use full name of `Python` built-in types.

    ```
    Returns:
        API version as integer.
    ```

* If not built-in `Python` type use backticks.

    ```
    Returns:
        Created user instance as `database.models.User`.
    ```

* If function returns multiple inconsistent values, write type first followed by description and type of returned values:

    ```
    Returns:
        A tuple containing predictions message as JSON and HTTP status code as integer.
    ```

* For simple `returns`, specify a description of what is returned followed by the type.

    ```
    Returns:
        Source estates' attributes as a list.
    ```

* For `if-else returns`, specify a description of what is returned followed by the type and conditional statement using `otherwise`.

    ```
    Returns:
        Source estates' attributes as a dictionary, if source estate is presented.
        Otherwise, None.
    ```

* For `if-elif-else returns`, specify a description of the value to be returned followed by its type,
as well as the corresponding conditional statement, for each conditional scenario.
For the last scenario, use `otherwise`.

    ```
    Returns:
        Source estates' attributes as a dictionary, if source estate is presented.
        Else fetched source estates from database as object, if source estate is not presented.
        Otherwise, None.
    ```

* If function returns nothing (for instance, it prints something), write:

    ```
    Returns:
        None.
    ```

## Tests

### Documentation

Use `case` and `except` test case documentation approach to clarify what test does and verify.

Class for testing:

```python
class User:

    def __init__(self):
        self._is_deleted = False

    @property
    def is_deleted(self):
        return self._is_deleted

    @is_deleted.setter
    def is_deleted(self, value):
        self._is_deleted = value

    def delete(self):
        self.is_deleted = True
```

And the corresponding test case:

```python
"""
Provide tests for user database model.
"""

def test_delete_user():
    """
    Case: delete user.
    Expect: user is marked as deleted.
    """
    user = User()
    user.delete()

    assert True == user.is_deleted
```

Use the following phrase to ensure you have proper documentation:

* when I `case phrase`,
* I expect that `expect phrase`.

So from the example above: when I delete user by identifier, I expect that user is marked as deleted. Basically, case
and except section should be written without technical details following business language and user stories (when I do
something, I await).

### Asserts

When you use assert statement with two arguments, place expected result first, then result from tested function.

```python
assert 17 == human.age
assert 5 == count_of_predictios
assert expected_result == result
assert expected_response == response
```

## Indentation

Separate code blocks with different logic by blank line to make code easier for reading.

```python
"""
Provide implementation of favorite file.
"""


class FavoriteFile:
    """
    Favorite file implementation.
    """

    @staticmethod
    async def mark(request):
        """
        Mark file as favorite.
        """
        arguments, errors = MarkFileAsFavoriteForm().load({
            'directory_id': request.match_info.get('directory_id'),
            'file_id': request.match_info.get('file_id'),
        })

        if errors:
            return json_response(error=errors, status_code=HTTPStatus.BAD_REQUEST)

        directory_id = arguments.get('directory_id')
        file_id = arguments.get('file_id')

        try:
            marked_file = MarkFile().as_favorite(request=request, directory_id=directory_id, file_id=file_id)

        except (DirectoryDoesNotExistError, FileDoesNotExistError) as error:
            return json_response(error=error.message, status_code=HTTPStatus.BAD_REQUEST)

        serialized_marked_file = FileSerializer().dump(marked_file).data

        return json_response(result=serialized_marked_file, status_code=HTTPStatus.OK)
```

### Not changeable summary lines

For the following methods in your code, please, keep summary lines similar to the following ones. If you want to add
detailed description, put to the detailed description section.

```python
def __init__(self):
    """
    Construct the object.
    """
```

```python
def setUp(self):
    """
    Set up data for tests.
    """
```

```python
def tearDown(self):
    """
    Destroy state after tests.
    """
```

## Paths

Whenever representing a path always use the class `Path` from the standard `pathlib` library.

For example, consider the following test helper that loads a fake response from a `JSON` file from a certain directory.

```python
from pathlib import Path


def get_fake_request(fixtures_directory: str, name: str) -> Dict[str, Any]:
    fixture_path = join(fixtures_directory, f'{name}.json')

    with open(fixture_path, 'r') as fixture_file:
        ...
```

This code should be written using `Path`.

```python
from pathlib import Path


def get_fake_request(fixtures_directory: Path, name: str) -> Dict[str, Any]:
    fixture_path = fixtures_directory / f'{name}.json'

    with fixture_path.open('r') as fixture_file:
        ...
```

Note that we not only create a `Path` to take advantages of its methods, but also to make the type annotations more precise.
So, any function parameter that represents a `path` should always be of type `Path` — even if we don't need any of
its methods.

## References

* Typing (standard library) - https://docs.python.org/3/library/typing.html
* mypy documentation - https://mypy.readthedocs.io/en/latest/
* Positional-only arguments - https://docs.python.org/3.7/library/inspect.html#inspect.Parameter.kind
* Positional-only arguments syntax - https://docs.python.org/3/glossary.html#term-parameter
