# Adding Op for NestedTensor Backend

This page covers references about adding a new Op for the NestedTensor backend.

There are a few options to add support. We usually want to follow these in order:

1.) My op is not covered and I feel like writing an implementation.

2.) My op is covered in the padded case and the overhead of converting to a padded tensor and back is worth it.

3.) I want an op covered and I don't care about performance 