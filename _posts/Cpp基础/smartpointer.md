Using smartpointer(boost::scoped_ptr)

error: no match for call to ‘(boost::scoped_ptr<> ... use reset
Reason is, if not called in constructor, needs ()operator. But smart pointer has no ()operator. So failed.
But declare in class, and call it in class initialization list is fine. Initialization list will use constructor.
http://stackoverflow.com/questions/17938399/boost-scoped-ptr-declaration-vs-allocation

Sometimes when don't want to call the constructor of some customized class, but need to use the object in some situation. Use a pointer pointing to new-created object.
e.g. pointer to SHM mutex


p.s.
shared_ptr is more expensive than scoped_ptr
