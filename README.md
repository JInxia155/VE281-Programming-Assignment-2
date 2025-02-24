Download Link : https://programming.engineering/product/ve281-programming-assignment-2/

# VE281-Programming-Assignment-2
VE281 — Programming Assignment 2
1 Introduction

Key objects are equal. It is used to determine which key-value pair should be returned when there is a hash collision, and it also help ensure the keys are unique in the hash table.

If you want to define your own hash function and key-equal function, you can refer to the following definitions:

    template<typename Key>

    struct hash<Key> {

    size_t operator()(const Key &key) const;

    }

5

    template<typename Key>

    struct equal_to<Key> {

    bool operator()(const Key &lhs, const Key &rhs) const;

    }

2.1.2 Number of Buckets

    typedef std::vector<HashNodeList> HashTableData;

    protected:

    HashTableData buckets;

    size_t tableSize;

    double maxLoadFactor;

    Hash hash;

    KeyEqual keyEqual;

    }

tableSize means the number of key-value pairs in the hash table. You can also add your own private / protected variables (or attributes) after these if necessary, but mostly the defined ones are enough. Here we use the protected keyword so that your HashTable class can be further inherited and overwritten for testing.

2.1.4 Iterators

We’re introducing iterators in this section. First of all, why you need to use iterators in this project? Supposing you want to find a key-value pair in the hash table, and then you may erase it from the hash table if it satisfies certain conditions. There are three implementations to achieved this:

    Use the find method to find a key-value pair, use the erase method to erase it.

    Use the find method to find a pointer to a key-value pair, use the erase method which accepts a pointer to erase it.

    Use the find method to find an iterator to a key-value pair, use the erase method which accepts an iterator to erase it.

In the first implementation, two lookups of the key is performed.

In the second implementation, only one lookup of the key is performed. However, the user can access the internal data structure of the hash table with the pointer, which is dangerous and violates the rule of packaging in Object-Oriented Programming.

In the third implementation, only one lookup of the key is performed as well, and the iterator only provides a restricted access to the key-value pair, so it’s a better solution.

In this project, we’ve already provided you with a completed iterator. You are going to use the iterator in the implementation of other methods in the hash table.

    class Iterator {

    private:

    typedef typename HashTableData::iterator VectorIterator;

    typedef typename HashNodeList::iterator ListIterator;

5

    const HashTable &hashTable;

    VectorIterator bucketIt;

    ListIterator listItBefore;

    bool endFlag = false;

    }

Here bucketIt means which bucket the iterator is in, listItBefore means the iterator pointer the “before” the key-value pair in the linked list.

We’ll give a simple explanation of the “before” iterator. If you want to erase a node in a linked list, you need to link the previous node and the next node. However, the list is single directional, so that you can’t get the previous node in O(1) time unless you’ve saved it. If you always use a “before” iterator, the deletion of node will be possible, and you can access the current node by “next” of the “before” iterator. This is a built-in functionality of the std::forward_list[2] class. The class also provides a before_begin method, which is different from other STL containers. You can check the documentation for more information.

Since the list is single directional (in order to save memory), the iterator only supports single directional iteration. The operator ++ is already overloaded for you. The iterator doesn’t have a const version, you can implement it if you’re interested in iterators, but it’s not mandatory. You can see the detail implementation of the iterator in the starter code.

We also defines a variable

    typename HashTableData::iterator firstBucketIt;

in the hash table. It provides an O(1) access to the the first key-value pair in the hash table. You need to update its value whenever an insert, erase or rehash operation is applied to the hash table.

What’s more, the hash table supports basic iteration and range-for loops[3] with the help of iterators and two methods:

begin and end. More specifically, with the C++98 standard you can iterate the hash table by:

    for (auto it = hashTable.begin(); it != hashTable.end(); ++it) {

    std::cout << it->first << ” “ << it->second << std::endl;

    }

With C++11, you can use the following as an alternative:

    for (auto &item : hashTable) {

    std::cout << item.first << ” “ << item.second << std::endl;

    }

With C++17, you can even do it in a more graceful way (similar to python and some other modern languages):

    for (auto &[key, value] : hashTable) {

    std::cout << key << ” “ << value << std::endl;

    }

The order of the output of the above code is arbitrary and is dependent on implementation. We’ll not test the internal order of the key-value pairs in your hash table (returned by iteration), since they’re meant to be “unordered”.

2.2 Comparison of Hash Table and Linked List

We also ask you to study the performances of hash table and linked list. Basically, you will compare these classes:

    HashTable

    std::unordered_map

    std::list or std::forward_list (their performances should be similar)

You can design your own comparison metric. For example, you can insert n key-value pairs into all these classes, then run k find, update, insert or erase operations. In a linked list, you can use linear search, it should be very simple to implement these operations with std::list or std::forward_list.

Write about one page on the report about your findings, plots and explanations.

2.2.1 Hints

    The performance of programs on Windows is usually not stable, so you should do the experiments on a Unix based system.

    You may want to write another program to do this study.

    You can use the C++11 pseudo-random number generation library to generate more randomized numbers (instead of using std::rand).

    You can use the C++11 chrono library to get more accurate runtime of functions than std::clock.

    (Optional) You can use GNU Perf (only available on Linux) to find the bottleneck of your implementation.

    Although the major factor that affects the runtime is the size of the input array, however, the runtime for an algorithm may also weakly depend on the detailed input array. Thus, for each size, you should generate a number of arrays of that size and obtain the mean runtime on all these arrays. Also, for fair comparison, the same set of arrays should be applied to all the data structures.

    You should try at least 5 representative sizes.

2.3 Study of Combining Hash Functions

In this part, you are going to do some literature study on how to combine two hash functions. More specifically, if the type of the key of the hash table is std::pair<T1, T2> and the hash functions are already defined for T1 and T2, how to combine the results of these two hash functions and write a new function hash<std::pair<T1, T2>>.

The definition and a trivial implementation (which is not good) of the combined hash function (object) is provided:

    struct PairHash {

    template<typename T1, typename T2>

    size_t operator()(const pair<T1, T2> &p) const {

    // TODO: this implementation is not good, improve it!

    return std::hash<T1>()(p.first) ^ std::hash<T2>()(p.second);

    }

    };

Improve the implementation of the PairHash above in your report. Make sure it can compile and work with HashTable and std::unordered_map. You should also give a brief explanation of one paragraph about why you choose this implementation. You can refer to any source (i.e., paper, open-source code), but you need to reference it in your report.

3 Implementation Requirements and Restrictions

3.1 Requirements

    You must make sure that your code compiles successfully on a Linux operating system with g++ and the options

-std=c++1z -Wconversion -Wall -Werror -Wextra -pedantic.

    You should not change the definitions of the functions and variables, as well as the public or protected keywords for them in hashtable.hpp.

    You can define helper functions and new variables, don’t forget to mark them as private or protected.

    You should only hand in one file hashtable.hpp.

    You can use any header file defined in the C++17 standard. You can use cppreference as a reference.

You only need to implement the methods (functions) marked with “TODO” in the file hashtable.hpp. Here is a list of the methods (functions):

    Copy Constructor and Assignment Constructor

    findMinimumBucketSize

    find

    insert

    erase

    operator[]

    rehash

Please refer to the descriptions of these functions in the starter code.

3.2 Memory Leak

Hint: You’re not going to use any dynamic memory allocation functions (new, malloc, etc.) directly in this project, thus it’s not possible to have memory leak in your program. This section is only for your reference.

You may not leak memory in any way. To help you see if you are leaking memory, you may wish to call valgrind, which can tell whether you have any memory leaks. (You need to install valgrind first if your system does not have this program.) The command to check memory leak is:

valgrind –leak-check=full <COMMAND>

You should replace <COMMAND> with the actual command you use to issue the program under testing. For example, if you want to check whether running program

./main < input.txt

causes memory leak, then <COMMAND> should be “./main < input.txt”. Thus, the command will be

valgrind –leak-check=full ./main < input.txt

4 Grading

Your program will be graded along five criteria:

4.1 Functional Correctness

Functional Correctness is determined by running a variety of test cases against your program, checking your solution using our automatic testing program.

4.2 Implementation Constraints

We will grade Implementation Constraints to see if you have met all of the implementation requirements and restrictions. In this project, we will also check whether your program has memory leak. For those programs that behave correctly but have memory leaks, we will deduct some points.

4.3 General Style

General Style refers to the ease with which TAs can read and understand your program, and the cleanliness and elegance of your code. Part of your grade will also be determined by the performance of your algorithm.

4.4 Performance

We will test your program with some large test cases. If your program is not able to finish within a reasonable amount of time, you will lose the performance score for those test cases.

4.5 Report on the performance study

Finally, we will also read your report and grade it based on the quality of your performance study.

5 Acknowledgement

The programming assignment is co-authored by Yihao Liu, an alumni of JI and the chief architect of JOJ.

References

    std::unordered_map – cppreference : https://en.cppreference.com/w/cpp/container/unordered_map

    std::forward_list – cppreference : https://en.cppreference.com/w/cpp/container/forward_list

    Range-based for loop – cppreference: https://en.cppreference.com/w/cpp/language/range-for

Appendix

hash_prime.hpp

    // adopted from

,→ /usr/include/c++/10.2.0/ext/pb_ds/detail/resize_policy/hash_prime_size_policy_imp.hpp

2

    #include <utility>

4

    namespace HashPrime {

    6
    	

    enum {
    		

    7
    	

    num_distinct_sizes_32_bit = 30,

    8
    	

    num_distinct_sizes_64_bit = 62,

    9
    	

    num_distinct_sizes = sizeof(std::size_t) != 8 ?

    10
    			

    num_distinct_sizes_32_bit : num_distinct_sizes_64_bit,

    11
    	

    };
    		

    12
    			

    13
    	

    // Originally taken from the SGI implementation; acknowledged in the docs.

    14
    	

    // Further modified (for 64 bits) from tr1’s hashtable.

    15
    	

    static constexpr
    	

    std::size_t g_a_sizes[num_distinct_sizes_64_bit] = {

    16
    	

    /* 0
    	

    */
    	

    5ul,

    17
    	

    /* 1
    	

    */
    	

    11ul,

    18
    	

    /* 2
    	

    */
    	

    23ul,

    19
    	

    /* 3
    	

    */
    	

    47ul,

    20
    	

    /* 4
    	

    */
    	

    97ul,

    21
    	

    /* 5
    	

    */
    	

    199ul,

    22
    	

    /* 6
    	

    */
    	

    409ul,

    23
    	

    /* 7
    	

    */
    	

    823ul,

    24
    	

    /* 8
    	

    */
    	

    1741ul,

    25
    	

    /* 9
    	

    */
    	

    3469ul,

    26
    	

    /* 10
    	

    */
    	

    6949ul,

    27
    	

    /* 11
    	

    */
    	

    14033ul,

    28
    	

    /* 12
    	

    */
    	

    28411ul,

    29
    	

    /* 13
    	

    */
    	

    57557ul,

    30
    	

    /* 14
    	

    */
    	

    116731ul,

    31
    	

    /* 15
    	

    */
    	

    236897ul,

    32
    	

    /* 16
    	

    */
    	

    480881ul,

    33
    	

    /* 17
    	

    */
    	

    976369ul,

    34
    	

    /* 18
    	

    */
    	

    1982627ul,

    35
    	

    /* 19
    	

    */
    	

    4026031ul,

    36
    	

    /* 20
    	

    */
    	

    8175383ul,

    37
    	

    /* 21
    	

    */
    	

    16601593ul,

    38
    	

    /* 22
    	

    */
    	

    33712729ul,

    39
    	

    /* 23
    	

    */
    	

    68460391ul,

    40
    	

    /* 24
    	

    */
    	

    139022417ul,

    41
    	

    /* 25
    	

    */
    	

    282312799ul,

    42
    	

    /* 26
    	

    */
    	

    573292817ul,

    43
    	

    /* 27
    	

    */
    	

    1164186217ul,

    44
    	

    /*
    	

    28
    	

    */
    	

    2364114217ul,

    45
    	

    /*
    	

    29
    	

    */
    	

    4294967291ul,

    /* 30*/ (std::size_t) 8589934583ull,

    /* 31*/ (std::size_t) 17179869143ull,

    /* 32*/ (std::size_t) 34359738337ull,

    /* 33*/ (std::size_t) 68719476731ull,

    /* 34*/ (std::size_t) 137438953447ull,

    /* 35*/ (std::size_t) 274877906899ull,

    /* 36*/ (std::size_t) 549755813881ull,

    /* 37*/ (std::size_t) 1099511627689ull,

    /* 38*/ (std::size_t) 2199023255531ull,

    /* 39*/ (std::size_t) 4398046511093ull,

    /* 40*/ (std::size_t) 8796093022151ull,

    /* 41*/ (std::size_t) 17592186044399ull,

    /* 42*/ (std::size_t) 35184372088777ull,

    /* 43*/ (std::size_t) 70368744177643ull,

    /* 44*/ (std::size_t) 140737488355213ull,

    /* 45*/ (std::size_t) 281474976710597ull,

    /* 46*/ (std::size_t) 562949953421231ull,

    /* 47*/ (std::size_t) 1125899906842597ull,

    /* 48*/ (std::size_t) 2251799813685119ull,

    /* 49*/ (std::size_t) 4503599627370449ull,

    /* 50*/ (std::size_t) 9007199254740881ull,

    /* 51*/ (std::size_t) 18014398509481951ull,

    /* 52*/ (std::size_t) 36028797018963913ull,

    /* 53*/ (std::size_t) 72057594037927931ull,

    /* 54*/ (std::size_t) 144115188075855859ull,

    /* 55*/ (std::size_t) 288230376151711717ull,

    /* 56*/ (std::size_t) 576460752303423433ull,

    /* 57*/ (std::size_t) 1152921504606846883ull,

    /* 58*/ (std::size_t) 2305843009213693951ull,

    /* 59*/ (std::size_t) 4611686018427387847ull,

    /* 60*/ (std::size_t) 9223372036854775783ull,

    /* 61*/ (std::size_t) 18446744073709551557ull,

    };

79

    }

hashtable.hpp

    #include “hash_prime.hpp”

2

    #include <exception>

    #include <functional>

    #include <vector>

    #include <forward_list>

7

    /**

    * The Hashtable class

    * The time complexity of functions are based on n and k

    * n is the size of the hashtable

    * k is the length of Key

    13
    	

    * @tparam Key
    	

    key type

    14
    	

    * @tparam Value
    	

    data type

    15
    	

    * @tparam Hash
    	

    function object, return the hash value of a key

    16
    	

    * @tparam KeyEqual
    	

    function object, return whether two keys are the same

    */

    template<

    typename Key, typename Value,

    typename Hash = std::hash<Key>,

    typename KeyEqual = std::equal_to<Key>

    >

    class HashTable {

    public:

    typedef std::pair<const Key, Value> HashNode;

    typedef std::forward_list<HashNode> HashNodeList;

    typedef std::vector<HashNodeList> HashTableData;

    			
    			
    			
    			
    			
    			
    			
    			
    		
    			
    			
    		
    			
    		
    			
    		
    			
    		
    			
    			

HashTable() :

    buckets(DEFAULT_BUCKET_SIZE), tableSize(0), ,→ maxLoadFactor(DEFAULT_LOAD_FACTOR),

    hash(Hash()), keyEqual(KeyEqual()) {

    firstBucketIt = buckets.end();

    }

182

    * Find the value in hashtable by key

    * If the key exists, iterator points to the corresponding value, and it.endFlag =

,→ false

    * Otherwise, iterator points to the place that the key were to be inserted, and

,→ it.endFlag = true

1 Introduction

Key objects are equal. It is used to determine which key-value pair should be returned when there is a hash collision, and it also help ensure the keys are unique in the hash table.

If you want to define your own hash function and key-equal function, you can refer to the following definitions:

    template<typename Key>

    struct hash<Key> {

    size_t operator()(const Key &key) const;

    }

5

    template<typename Key>

    struct equal_to<Key> {

    bool operator()(const Key &lhs, const Key &rhs) const;

    }

2.1.2 Number of Buckets

    typedef std::vector<HashNodeList> HashTableData;

    protected:

    HashTableData buckets;

    size_t tableSize;

    double maxLoadFactor;

    Hash hash;

    KeyEqual keyEqual;

    }

tableSize means the number of key-value pairs in the hash table. You can also add your own private / protected variables (or attributes) after these if necessary, but mostly the defined ones are enough. Here we use the protected keyword so that your HashTable class can be further inherited and overwritten for testing.

2.1.4 Iterators

We’re introducing iterators in this section. First of all, why you need to use iterators in this project? Supposing you want to find a key-value pair in the hash table, and then you may erase it from the hash table if it satisfies certain conditions. There are three implementations to achieved this:

    Use the find method to find a key-value pair, use the erase method to erase it.

    Use the find method to find a pointer to a key-value pair, use the erase method which accepts a pointer to erase it.

    Use the find method to find an iterator to a key-value pair, use the erase method which accepts an iterator to erase it.

In the first implementation, two lookups of the key is performed.

In the second implementation, only one lookup of the key is performed. However, the user can access the internal data structure of the hash table with the pointer, which is dangerous and violates the rule of packaging in Object-Oriented Programming.

In the third implementation, only one lookup of the key is performed as well, and the iterator only provides a restricted access to the key-value pair, so it’s a better solution.

In this project, we’ve already provided you with a completed iterator. You are going to use the iterator in the implementation of other methods in the hash table.

    class Iterator {

    private:

    typedef typename HashTableData::iterator VectorIterator;

    typedef typename HashNodeList::iterator ListIterator;

5

    const HashTable &hashTable;

    VectorIterator bucketIt;

    ListIterator listItBefore;

    bool endFlag = false;

    }

Here bucketIt means which bucket the iterator is in, listItBefore means the iterator pointer the “before” the key-value pair in the linked list.

We’ll give a simple explanation of the “before” iterator. If you want to erase a node in a linked list, you need to link the previous node and the next node. However, the list is single directional, so that you can’t get the previous node in O(1) time unless you’ve saved it. If you always use a “before” iterator, the deletion of node will be possible, and you can access the current node by “next” of the “before” iterator. This is a built-in functionality of the std::forward_list[2] class. The class also provides a before_begin method, which is different from other STL containers. You can check the documentation for more information.

Since the list is single directional (in order to save memory), the iterator only supports single directional iteration. The operator ++ is already overloaded for you. The iterator doesn’t have a const version, you can implement it if you’re interested in iterators, but it’s not mandatory. You can see the detail implementation of the iterator in the starter code.

We also defines a variable

    typename HashTableData::iterator firstBucketIt;

in the hash table. It provides an O(1) access to the the first key-value pair in the hash table. You need to update its value whenever an insert, erase or rehash operation is applied to the hash table.

What’s more, the hash table supports basic iteration and range-for loops[3] with the help of iterators and two methods:

begin and end. More specifically, with the C++98 standard you can iterate the hash table by:

    for (auto it = hashTable.begin(); it != hashTable.end(); ++it) {

    std::cout << it->first << ” “ << it->second << std::endl;

    }

With C++11, you can use the following as an alternative:

    for (auto &item : hashTable) {

    std::cout << item.first << ” “ << item.second << std::endl;

    }

With C++17, you can even do it in a more graceful way (similar to python and some other modern languages):

    for (auto &[key, value] : hashTable) {

    std::cout << key << ” “ << value << std::endl;

    }

The order of the output of the above code is arbitrary and is dependent on implementation. We’ll not test the internal order of the key-value pairs in your hash table (returned by iteration), since they’re meant to be “unordered”.

2.2 Comparison of Hash Table and Linked List

We also ask you to study the performances of hash table and linked list. Basically, you will compare these classes:

    HashTable

    std::unordered_map

    std::list or std::forward_list (their performances should be similar)

You can design your own comparison metric. For example, you can insert n key-value pairs into all these classes, then run k find, update, insert or erase operations. In a linked list, you can use linear search, it should be very simple to implement these operations with std::list or std::forward_list.

Write about one page on the report about your findings, plots and explanations.

2.2.1 Hints

    The performance of programs on Windows is usually not stable, so you should do the experiments on a Unix based system.

    You may want to write another program to do this study.

    You can use the C++11 pseudo-random number generation library to generate more randomized numbers (instead of using std::rand).

    You can use the C++11 chrono library to get more accurate runtime of functions than std::clock.

    (Optional) You can use GNU Perf (only available on Linux) to find the bottleneck of your implementation.

    Although the major factor that affects the runtime is the size of the input array, however, the runtime for an algorithm may also weakly depend on the detailed input array. Thus, for each size, you should generate a number of arrays of that size and obtain the mean runtime on all these arrays. Also, for fair comparison, the same set of arrays should be applied to all the data structures.

    You should try at least 5 representative sizes.

2.3 Study of Combining Hash Functions

In this part, you are going to do some literature study on how to combine two hash functions. More specifically, if the type of the key of the hash table is std::pair<T1, T2> and the hash functions are already defined for T1 and T2, how to combine the results of these two hash functions and write a new function hash<std::pair<T1, T2>>.

The definition and a trivial implementation (which is not good) of the combined hash function (object) is provided:

    struct PairHash {

    template<typename T1, typename T2>

    size_t operator()(const pair<T1, T2> &p) const {

    // TODO: this implementation is not good, improve it!

    return std::hash<T1>()(p.first) ^ std::hash<T2>()(p.second);

    }

    };

Improve the implementation of the PairHash above in your report. Make sure it can compile and work with HashTable and std::unordered_map. You should also give a brief explanation of one paragraph about why you choose this implementation. You can refer to any source (i.e., paper, open-source code), but you need to reference it in your report.

3 Implementation Requirements and Restrictions

3.1 Requirements

    You must make sure that your code compiles successfully on a Linux operating system with g++ and the options

-std=c++1z -Wconversion -Wall -Werror -Wextra -pedantic.

    You should not change the definitions of the functions and variables, as well as the public or protected keywords for them in hashtable.hpp.

    You can define helper functions and new variables, don’t forget to mark them as private or protected.

    You should only hand in one file hashtable.hpp.

    You can use any header file defined in the C++17 standard. You can use cppreference as a reference.

You only need to implement the methods (functions) marked with “TODO” in the file hashtable.hpp. Here is a list of the methods (functions):

    Copy Constructor and Assignment Constructor

    findMinimumBucketSize

    find

    insert

    erase

    operator[]

    rehash

Please refer to the descriptions of these functions in the starter code.

3.2 Memory Leak

Hint: You’re not going to use any dynamic memory allocation functions (new, malloc, etc.) directly in this project, thus it’s not possible to have memory leak in your program. This section is only for your reference.

You may not leak memory in any way. To help you see if you are leaking memory, you may wish to call valgrind, which can tell whether you have any memory leaks. (You need to install valgrind first if your system does not have this program.) The command to check memory leak is:

valgrind –leak-check=full <COMMAND>

You should replace <COMMAND> with the actual command you use to issue the program under testing. For example, if you want to check whether running program

./main < input.txt

causes memory leak, then <COMMAND> should be “./main < input.txt”. Thus, the command will be

valgrind –leak-check=full ./main < input.txt

4 Grading

Your program will be graded along five criteria:

4.1 Functional Correctness

Functional Correctness is determined by running a variety of test cases against your program, checking your solution using our automatic testing program.

4.2 Implementation Constraints

We will grade Implementation Constraints to see if you have met all of the implementation requirements and restrictions. In this project, we will also check whether your program has memory leak. For those programs that behave correctly but have memory leaks, we will deduct some points.

4.3 General Style

General Style refers to the ease with which TAs can read and understand your program, and the cleanliness and elegance of your code. Part of your grade will also be determined by the performance of your algorithm.

4.4 Performance

We will test your program with some large test cases. If your program is not able to finish within a reasonable amount of time, you will lose the performance score for those test cases.

4.5 Report on the performance study

Finally, we will also read your report and grade it based on the quality of your performance study.

5 Acknowledgement

The programming assignment is co-authored by Yihao Liu, an alumni of JI and the chief architect of JOJ.

References

    std::unordered_map – cppreference : https://en.cppreference.com/w/cpp/container/unordered_map

    std::forward_list – cppreference : https://en.cppreference.com/w/cpp/container/forward_list

    Range-based for loop – cppreference: https://en.cppreference.com/w/cpp/language/range-for

Appendix

hash_prime.hpp

    // adopted from

,→ /usr/include/c++/10.2.0/ext/pb_ds/detail/resize_policy/hash_prime_size_policy_imp.hpp

2

    #include <utility>

4

    namespace HashPrime {

    6
    	

    enum {
    		

    7
    	

    num_distinct_sizes_32_bit = 30,

    8
    	

    num_distinct_sizes_64_bit = 62,

    9
    	

    num_distinct_sizes = sizeof(std::size_t) != 8 ?

    10
    			

    num_distinct_sizes_32_bit : num_distinct_sizes_64_bit,

    11
    	

    };
    		

    12
    			

    13
    	

    // Originally taken from the SGI implementation; acknowledged in the docs.

    14
    	

    // Further modified (for 64 bits) from tr1’s hashtable.

    15
    	

    static constexpr
    	

    std::size_t g_a_sizes[num_distinct_sizes_64_bit] = {

    16
    	

    /* 0
    	

    */
    	

    5ul,

    17
    	

    /* 1
    	

    */
    	

    11ul,

    18
    	

    /* 2
    	

    */
    	

    23ul,

    19
    	

    /* 3
    	

    */
    	

    47ul,

    20
    	

    /* 4
    	

    */
    	

    97ul,

    21
    	

    /* 5
    	

    */
    	

    199ul,

    22
    	

    /* 6
    	

    */
    	

    409ul,

    23
    	

    /* 7
    	

    */
    	

    823ul,

    24
    	

    /* 8
    	

    */
    	

    1741ul,

    25
    	

    /* 9
    	

    */
    	

    3469ul,

    26
    	

    /* 10
    	

    */
    	

    6949ul,

    27
    	

    /* 11
    	

    */
    	

    14033ul,

    28
    	

    /* 12
    	

    */
    	

    28411ul,

    29
    	

    /* 13
    	

    */
    	

    57557ul,

    30
    	

    /* 14
    	

    */
    	

    116731ul,

    31
    	

    /* 15
    	

    */
    	

    236897ul,

    32
    	

    /* 16
    	

    */
    	

    480881ul,

    33
    	

    /* 17
    	

    */
    	

    976369ul,

    34
    	

    /* 18
    	

    */
    	

    1982627ul,

    35
    	

    /* 19
    	

    */
    	

    4026031ul,

    36
    	

    /* 20
    	

    */
    	

    8175383ul,

    37
    	

    /* 21
    	

    */
    	

    16601593ul,

    38
    	

    /* 22
    	

    */
    	

    33712729ul,

    39
    	

    /* 23
    	

    */
    	

    68460391ul,

    40
    	

    /* 24
    	

    */
    	

    139022417ul,

    41
    	

    /* 25
    	

    */
    	

    282312799ul,

    42
    	

    /* 26
    	

    */
    	

    573292817ul,

    43
    	

    /* 27
    	

    */
    	

    1164186217ul,

    44
    	

    /*
    	

    28
    	

    */
    	

    2364114217ul,

    45
    	

    /*
    	

    29
    	

    */
    	

    4294967291ul,

    /* 30*/ (std::size_t) 8589934583ull,

    /* 31*/ (std::size_t) 17179869143ull,

    /* 32*/ (std::size_t) 34359738337ull,

    /* 33*/ (std::size_t) 68719476731ull,

    /* 34*/ (std::size_t) 137438953447ull,

    /* 35*/ (std::size_t) 274877906899ull,

    /* 36*/ (std::size_t) 549755813881ull,

    /* 37*/ (std::size_t) 1099511627689ull,

    /* 38*/ (std::size_t) 2199023255531ull,

    /* 39*/ (std::size_t) 4398046511093ull,

    /* 40*/ (std::size_t) 8796093022151ull,

    /* 41*/ (std::size_t) 17592186044399ull,

    /* 42*/ (std::size_t) 35184372088777ull,

    /* 43*/ (std::size_t) 70368744177643ull,

    /* 44*/ (std::size_t) 140737488355213ull,

    /* 45*/ (std::size_t) 281474976710597ull,

    /* 46*/ (std::size_t) 562949953421231ull,

    /* 47*/ (std::size_t) 1125899906842597ull,

    /* 48*/ (std::size_t) 2251799813685119ull,

    /* 49*/ (std::size_t) 4503599627370449ull,

    /* 50*/ (std::size_t) 9007199254740881ull,

    /* 51*/ (std::size_t) 18014398509481951ull,

    /* 52*/ (std::size_t) 36028797018963913ull,

    /* 53*/ (std::size_t) 72057594037927931ull,

    /* 54*/ (std::size_t) 144115188075855859ull,

    /* 55*/ (std::size_t) 288230376151711717ull,

    /* 56*/ (std::size_t) 576460752303423433ull,

    /* 57*/ (std::size_t) 1152921504606846883ull,

    /* 58*/ (std::size_t) 2305843009213693951ull,

    /* 59*/ (std::size_t) 4611686018427387847ull,

    /* 60*/ (std::size_t) 9223372036854775783ull,

    /* 61*/ (std::size_t) 18446744073709551557ull,

    };

79

    }

hashtable.hpp

    #include “hash_prime.hpp”

2

    #include <exception>

    #include <functional>

    #include <vector>

    #include <forward_list>

7

    /**

    * The Hashtable class

    * The time complexity of functions are based on n and k

    * n is the size of the hashtable

    * k is the length of Key

    13
    	

    * @tparam Key
    	

    key type

    14
    	

    * @tparam Value
    	

    data type

    15
    	

    * @tparam Hash
    	

    function object, return the hash value of a key

    16
    	

    * @tparam KeyEqual
    	

    function object, return whether two keys are the same

    */

    template<

    typename Key, typename Value,

    typename Hash = std::hash<Key>,

    typename KeyEqual = std::equal_to<Key>

    >

    class HashTable {

    public:

    typedef std::pair<const Key, Value> HashNode;

    typedef std::forward_list<HashNode> HashNodeList;

    typedef std::vector<HashNodeList> HashTableData;

    			
    			
    			
    			
    			
    			
    			
    			
    		
    			
    			
    		
    			
    		
    			
    		
    			
    		
    			
    			

HashTable() :

    buckets(DEFAULT_BUCKET_SIZE), tableSize(0), ,→ maxLoadFactor(DEFAULT_LOAD_FACTOR),

    hash(Hash()), keyEqual(KeyEqual()) {

    firstBucketIt = buckets.end();

    }

182

    * Find the value in hashtable by key

    * If the key exists, iterator points to the corresponding value, and it.endFlag =

,→ false

    * Otherwise, iterator points to the place that the key were to be inserted, and

,→ it.endFlag = true
