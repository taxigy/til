# Sending multipart data

To test your API endpoint that expects multipart data, use `-F`:

```bash
echo "I am a file" > file.txt
curl -X POST http://localhost:3000/whatever -F one="just a string" -F two=@file.txt
```

Here, `one` is just a string and will be delivered exactly as is, while `two` is
a file that will be piped from current location in the file system (./file.txt)
and its content sent to server.
