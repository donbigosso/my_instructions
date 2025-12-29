# PHP useful code #
## File and folder management ##
### Getting "formatted" file content (with line breaks) ###
```
$folder = __DIR__ . '/uploads';
$filexx = $folder . '/test_file.txt';
nl2br(file_get_contents($filexx));
```
### Checking if file exists ###
```
file_exists($file2);
```

### Write into file ###
```
file_put_contents($file, "Hello from PHP!\n");
```
Optionally change the permissions in Linux, so that group can also write:
``` chmod($file, 0664);  // owner rw, group rw, others r ```