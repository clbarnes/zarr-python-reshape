# zarr-python-reshape

A reshape codec for zarr-python implementing the [Zarr reshape codec extension](https://github.com/zarr-developers/zarr-extensions/tree/main/codecs/reshape).

## Installation

```bash
pip install zarr-reshape
```

## Usage

```python
import zarr
import zarr_reshape  # Registers the codec

# Create an array with the reshape codec
# This reshapes chunks from (100, 50, 64, 3) to (5000, 64, 3)
arr = zarr.create_array(
    store="my_array.zarr",
    shape=(1000, 500, 64, 3),
    chunks=(100, 50, 64, 3),
    dtype="float32",
    filters=[
        zarr_reshape.ReshapeCodec(shape=[[0, 1], [2], 3])
    ],
)
```

## Shape Configuration

The `shape` configuration parameter specifies how to compute the output shape from the input shape. Each element can be:

- **A positive integer**: An explicit size for that dimension.
- **A list of integers**: Input dimension indices whose product determines the output dimension size.
- **The value `-1`**: Inferred automatically to satisfy `prod(output_shape) == prod(input_shape)`. May occur at most once.

### Examples

For an input chunk of shape `(100, 50, 64, 3)`:

| Shape Configuration | Output Shape | Description |
|---------------------|--------------|-------------|
| `[[0, 1], [2], 3]` | `(5000, 64, 3)` | Merge first two dimensions |
| `[10, 10, -1]` | `(10, 10, 96000)` | Fixed first two, infer third |
| `[[0], [1], [2], [3]]` | `(100, 50, 64, 3)` | Identity reshape |
| `[-1]` | `(960000,)` | Flatten to 1D |

### Constraints

- Input dimensions referenced in lists must be in strictly monotonically increasing order.
- The invariant `prod(output_shape) == prod(input_shape)` must be satisfiable.

## AI statement

Claude Code was used in the initial creation of this package.
