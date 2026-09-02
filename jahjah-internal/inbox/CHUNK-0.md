SHA256: 76eec0d08f191edb5d79e70c7ff6906f4ac162f4d6c410711a9d6c969f451159
TEST FILE — chunk 3 T4 acceptance test. This must be REFUSED.

SESSION: test
MODEL: claude-opus-5

It carries a VALID SHA256 header and a valid MODEL line, so the only thing wrong with it is the
session name. If the lane starts this, the SESSION check does not work.
