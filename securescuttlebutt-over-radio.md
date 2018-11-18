Notes outline and stuff
Say something about using git bash on windows. The sbot command wont work because for whatever reason it is sending this to the commnd
Marnee@DESKTOP-BBKBQMF MINGW64 ~/AppData/Roaming/npm/node_modules/scuttlebot/lib
$ sbot createUserStream --id @/e81Vsfar9p/0TxZaAZvVN+M4Yd0i0aFTcsGgFgblls=.ed25519
UsageError: Param 0 must have a .id of type "@C:/Program Files/Git/e81Vsfar9p/0TxZaAZvVN+M4Yd0i0aFTcsGgFgblls=.ed25519"

You modified the validator in scuttlebot to return the v.id string
that how you know what happened