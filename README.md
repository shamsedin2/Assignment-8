class Contact:
    """A simple class to store contact information."""
    
    def __init__(self, name, number):
        """
        Initialize a Contact with a name and phone number.
        
        Args:
            name (str): The contact's name
            number (str): The contact's phone number (e.g., "123-456-7890")
        """
        self.name = name
        self.number = number
    
    def __str__(self):
        """Return the contact's information in the format 'NAME: NUMBER'."""
        return f"{self.name}: {self.number}"


class Node:
    """A node for use in a linked list for collision handling."""
    
    def __init__(self, key, value):
        """
        Initialize a Node with a key-value pair.
        
        Args:
            key (str): The contact's name (used as the key)
            value (Contact): The Contact object
        """
        self.key = key
        self.value = value
        self.next = None


class HashTable:
    """A hash table implementation using separate chaining for collision handling."""
    
    def __init__(self, size=10):
        """
        Initialize a HashTable with a fixed size.
        
        Args:
            size (int): The size of the underlying array (default: 10)
        """
        self.size = size
        self.data = [None] * size
    
    def hash_function(self, key):
        """
        Convert a string key into an array index.
        
        Args:
            key (str): The key to hash (contact name)
            
        Returns:
            int: The array index for this key
        """
        # Simple hash function: sum ASCII values of characters mod table size
        hash_value = 0
        for char in key:
            hash_value += ord(char)
        return hash_value % self.size
    
    def insert(self, key, value):
        """
        Insert a contact into the hash table or update if key exists.
        
        Args:
            key (str): The contact's name
            value (str): The contact's phone number
        """
        # Create a Contact object
        contact = Contact(key, value)
        
        # Get the index using the hash function
        index = self.hash_function(key)
        
        # Create a new node
        new_node = Node(key, contact)
        
        # If the slot is empty, insert the new node
        if self.data[index] is None:
            self.data[index] = new_node
        else:
            # Handle collision: traverse the linked list
            current = self.data[index]
            
            # Check if the key already exists (update case)
            while current:
                if current.key == key:
                    # Update the existing contact's number
                    current.value = contact
                    return
                
                # If we're at the end of the list, break
                if current.next is None:
                    break
                current = current.next
            
            # Add the new node at the end of the linked list
            current.next = new_node
    
    def search(self, key):
        """
        Search for a contact by name.
        
        Args:
            key (str): The contact's name to search for
            
        Returns:
            Contact: The Contact object if found, None otherwise
        """
        # Get the index using the hash function
        index = self.hash_function(key)
        
        # Traverse the linked list at this index
        current = self.data[index]
        
        while current:
            if current.key == key:
                return current.value
            current = current.next
        
        # Contact not found
        return None
    
    def print_table(self):
        """Display the structure of the hash table."""
        for i in range(self.size):
            if self.data[i] is None:
                print(f"Index {i}: Empty")
            else:
                # Traverse the linked list and print all contacts
                contacts = []
                current = self.data[i]
                while current:
                    contacts.append(str(current.value))
                    current = current.next
                print(f"Index {i}: - {' - '.join(contacts)}")


# Test the implementation
if __name__ == "__main__":
    print("=== Testing HashTable Implementation ===\n")
    
    # Test 1: Create an empty hash table
    print("Test 1: Creating empty hash table")
    table = HashTable(10)
    table.print_table()
    
    print("\n" + "="*50 + "\n")
    
    # Test 2: Insert some contacts
    print("Test 2: Inserting contacts")
    table.insert("John", "909-876-1234")
    table.insert("Rebecca", "111-555-0002")
    table.print_table()
    
    print("\n" + "="*50 + "\n")
    
    # Test 3: Search for a contact
    print("Test 3: Searching for John")
    contact = table.search("John")
    print(f"Search result: {contact}")
    
    print("\n" + "="*50 + "\n")
    
    # Test 4: Handle collisions
    print("Test 4: Testing collision handling")
    table.insert("Amy", "111-222-3333")
    table.insert("May", "222-333-1111")
    table.print_table()
    
    print("\n" + "="*50 + "\n")
    
    # Test 5: Update existing contact
    print("Test 5: Updating Rebecca's number")
    table.insert("Rebecca", "999-444-9999")
    table.print_table()
    
    print("\n" + "="*50 + "\n")
    
    # Test 6: Search for non-existent contact
    print("Test 6: Searching for non-existent contact")
    result = table.search("Chris")
    print(f"Search result for Chris: {result}")


"""
DESIGN MEMO

Why is a hash table the right structure for fast lookups?

Hash tables provide O(1) average-case time complexity for search, insert, and delete 
operations, making them ideal for contact management systems where quick lookups are 
essential. By using a hash function to convert names into array indices, we can directly 
access contacts without iterating through all entries. This is significantly faster than 
linear search in lists (O(n)) or even binary search in sorted arrays (O(log n)).

How did you handle collisions?

I implemented separate chaining using linked lists. When multiple contacts hash to the 
same index, they're stored in a linked list at that position. Each Node contains the key 
(name), value (Contact object), and a pointer to the next node. When inserting, if a 
collision occurs, the new contact is added to the end of the chain. When searching, we 
traverse the linked list at the hashed index until we find the matching name or reach 
the end.

When might an engineer choose a hash table over a list or tree?

An engineer should choose a hash table when fast lookups by key are the primary 
requirement and the data doesn't need to be ordered. Hash tables excel in scenarios 
like contact management, caching, and database indexing. Lists are better when order 
matters and you need frequent iteration or when the dataset is small. Trees (like BSTs) 
are preferable when you need sorted data, range queries, or guaranteed O(log n) 
worst-case performance. Hash tables offer the best average performance for key-based 
access but don't maintain ordering and can have degraded performance with poor hash 
functions or high collision rates.
""" "Debugged final output issues"
