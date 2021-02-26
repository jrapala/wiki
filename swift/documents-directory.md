# Reading and Writing to the Documents Directory

Store to the document directory if you're writing a lot of data. This syncs with iCloud as well.



1. You'll need to get the document directory for the current user:

   ```swift
   func getDocumentsDirectory() -> URL {
       // find all possible documents directories for this user
       let paths = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)
   
       // if there's multiple dirs, we only really care about the first one
       return paths[0]
   }
   ```

   

2. Writing takes three parameters: url to write to, whether it's atomic (write all at once), and the character encoding. Should almost always be atomic, which writes to a temporary file first.

   ```swift
   Text("Hello World")
       .onTapGesture {
           let str = "Test Message"
           let url = self.getDocumentsDirectory().appendingPathComponent("message.txt")
   
           do {
               try str.write(to: url, atomically: true, encoding: .utf8)
               let input = try String(contentsOf: url)
               print(input)
           } catch {
               print(error.localizedDescription)
           }
       }
   ```

   