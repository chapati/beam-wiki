In Merkle trees there are leaf nodes that are added explicitly, and appropriate non-leaf nodes which are created on-demand.

Our DMMR consists of _Elements_ of variable size, whereas each new element internally contains all the supposed non-leaf nodes. Technically element consists of:

1. Hashes of the assumed non-leaf nodes
1. _Pointers_ to the elements that contain the siblings assumed by the above non-leaf nodes.
   * By _pointers_ we mean the information used to access the element, not necessarily the memory pointer.
1. A pointer to the last element of the previous MMR peak.


For example, consider and MMR containing 10 items:

                             *
                            / \
                           /   \
                          /     \
                         /       \
                        /         \
                       /           \
                      /             \
                     /               \
                    /                 \
                   /                   \
                  /                     \
                 *                       *
                / \                     / \
               /   \                   /   \
              /     \                 /     \
             /       \               /       \
            /         \             /         \
           *           *           *           *           *
          / \         / \         / \         / \         / \
         /   \       /   \       /   \       /   \       /   \
        0     1     2     3     4     5     6     7     8     9

In our DMMR this is represented by the following data:

