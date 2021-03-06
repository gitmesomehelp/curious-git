###################
Reading git objects
###################

By default, git stores objects in ``.git/objects`` as their original contents,
compressed, and prepended with a few extra characters (see below).

These are easy to read with a small amount of code.

git will read the files for you with ``git show`` or ``git cat-file``.

When a repository gets larger, git may use another, less simple format to
store the data, called *packfiles*, in which case these simple code fragments
won't work.  The fragments are to show the simplicity of the default way that
git stores objects.

git uses a library called ``zlib`` for compressing the objects.

To show how this works, first generate a small git repository to make an
object file:

.. workrun::
    :hide:

    rm -rf git_example

.. workrun::

    # Make a new empty git repository
    mkdir git_example
    cd git_example
    git init

.. workrun::
    :cwd: /working/git_example

    # Make and stage a file, to create an object in .git/objects
    echo "My data fits on one line" > my_file.txt
    git add my_file.txt

.. workrun::
    :cwd: /working/git_example

    # Show the .git/objects directory
    tree -a .git/objects

We can read this object using some simple Python code.  Start up the Python
console and:

>>> import zlib  # A compression / decompression library
>>> filename = 'working/git_example/.git/objects/e2/7bb34b0807ebf1b91bb66a4c147430cde4f08f'
>>> compressed_contents = open(filename, 'rb').read()
>>> decompressed_contents = zlib.decompress(compressed_contents)

>>> decompressed_contents
'blob 25\x00My data fits on one line\n'

The contents of the object file is the original contents prepended by ``blob
25`` and a 0 byte, where 25 is the number of characters in the file, not
including the housekeeping string:

>>> len(decompressed_contents)  # (length of housekeeping string = 8) + 25
33

The hash value for the file is the hash of the decompressed file, including
the housekeeping string:

>>> from hashlib import sha1  # SHA1 hash algorithm
>>> hash_value = sha1(decompressed_contents).hexdigest()
>>> hash_value
'e27bb34b0807ebf1b91bb66a4c147430cde4f08f'
