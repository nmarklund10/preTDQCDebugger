# Install Google Test

This project uses Google Test and Google Mock to implement unit testing.
Installation of these libraries is necessary in order to properly unit test
the code.


## Google Test on Ubuntu 16.04

```
$ sudo apt install libgtest-dev
$ cd /usr/src/gtest
$ sudo mkdir build
$ cd build
$ sudo cmake ..
$ sudo make
$ sudo cp *.a /usr/lib
$ cd ..
$ sudo rm -rf build
```

Additional instructions
[here](https://www.eriksmistad.no/getting-started-with-google-test-on-ubuntu/)

## Google Test on Ubuntu 18.04

```
$ sudo apt-get install libgtest-dev
$ cd /usr/src/googletest/googletest
$ sudo mkdir build
$ cd build
$ sudo cmake ..
$ sudo make
$ sudo cp libgtest* /usr/lib/
$ cd ..
$ sudo rm -rf build
$ sudo mkdir /usr/local/lib/googletest
$ sudo ln -s /usr/lib/libgtest.a /usr/local/lib/googletest/libgtest.a
$ sudo ln -s /usr/lib/libgtest_main.a /usr/local/lib/googletest/libgtest_main.a
```

Additional instructions 
[here](https://gist.github.com/Cartexius/4c437c084d6e388288201aadf9c8cdd5)

## Google Mock on Ubuntu 18.04

After installing Google Test:

```
$ cd /usr/src/googlemock/googlemock
$ sudo mkdir build
$ cd build
$ sudo cmake ..
$ sudo make
$ sudo cp libgmock* /usr/lib/
$ cd ..
$ sudo rm -rf build
$ sudo mkdir /usr/local/lib/googlemock
$ sudo ln -s /usr/lib/libgmock.a /usr/local/lib/googlemock:/libgmock.a
$ sudo ln -s /usr/lib/libgtest_main.a /usr/local/lib/googletest/libgtest_main.a
```
