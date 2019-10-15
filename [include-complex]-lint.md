Don't include `<complex.h>` or `<ccomplex>` in your code. The [C99 spec](https://pubs.opengroup.org/onlinepubs/009695399/basedefs/complex.h.html) insists that these files shall define a macro `I`, which interferes with any code that wants to use `I` as a template parameter or just in general.

Prefer the C++ complex numeric library: `#include <complex>`.