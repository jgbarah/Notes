# Python tricks

## Substituting a method (function) by a mock in a test

There are many ways, but find here two that I found useful.

* Using the `@patch.object` decorator.

`ClassToPatch.method_to_patch` is the method to patch. `mock_method` is the method used to patch it. `autospec` is important to have a mocking method with the same attributes (and methods, it we were patching a class) than the patched object. In the test, mock is the mock (created by the decorator, based on `mock_method` and the spec of the method to patch):

```python
from unittest.mock import patch

def mock_method(arg1, arg2...):
    ...

    @patch.object(ClassToPatch, 'method_to_patch', side_effect=mock_method,
        autospec=True)
    def test_something(self, mock):
    	...
	# Check that the mock was called count times
        self.assertEqual(mock.call_count, count)
```

* Using a `patch.object` context.

Same as above, but instead of the decorator on the testing function, I use a patch context:

```
    def test_something(self):
        with patch.object(ClassToPatch, 'method_to_patch',
	    side_effect=mock_method, autospec=True) as mock:
	    ...
	    # Check that the mock was called count times
            self.assertEqual(mock.call_count, count)

```