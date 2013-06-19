# JSON comments proposal.
This is proposal to make comments in Json possible by creating subset of Json with changes described lower.

## Main ideas

- Comment can be placed _only_ in object or array.
- Comments is part of document.

## Comments in hashes

In object it must be after key-value pair. Like this:

    {
        "key": "value"
        /*
            Some
            Comment
        */
        /*
            Some
            Other
            Comment
        */
        //Some line comment
        //Another one
    }


It must be converts to this:

    {
        "key": "value",
        "/**/key": "\n\t\tSome\n\t\tComment\n",
        "/**//**/key": "\n\t\tSome\n\t\tOther\n\t\tComment\n",
        "///**//**/key": "Some line comment\nAnother one\n"
    }

Comments is citizens like key-value pairs and array values, but they doesn't require comma for separation.

Key in comment key-value pair is string `/**/` or `//` with last key in current scope attached.

So, for example:

    {
        /*some comment*/
        //some comment
        "test": {
            /*some comment*/
            //some comment
        }
    }

Converts to

    {
        "/**/": "some comment",
        "///**/": "some comment",
        "test": {
            "/**/": "some comment"
            "///**/": "some comment"
        }
    }


Several line comments should be concated in one comment key-value pair.

    {
        //test
        //test
        //test
        //test
    }

Converts to

    {
        "//": "test\ntest\ntest\ntest\n"
    }


## Comments in arrays.

Very important part of proposal is comments in arrays.
Comment here must be hash, with same content as is in key-value pair higher.

    [
        //ToDo: better comment jokes
        "value1",
        "value2"
        /*This is array of values */
    ]

Converts to:

    [
        {"//": 'ToDo: better comment jokes'},
        "value1",
        "value2",
        {"/**/": 'This is array of values'}
    ]

No attaches uses in arrays.


## Where you can't put comments


You can't put it inside key-value pair. This is invalid:

    {
        "key": /* comment */ "value"
    }

You also can't put it inside strings, but this is obvious, isn't it?

    {
        "key": "/* This would be just text*/"
    }

And it would be bad decision to put it in numbers and words.

    {
        "key": tr/* I'm throwning an error! */ue
    }

or

    {
        "key": 4/* How do you even occurred to put comment here? */2
    }

Same with line comments.

## Todo

- Helper functions for iterating throw json arrays/hashes with comments
