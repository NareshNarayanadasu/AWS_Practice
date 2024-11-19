To connect a web app running on your local system to a MySQL database hosted on a different virtual machine (VM), you’ll need to follow several steps. Here’s a general guide:

### 1. **Setup MySQL on the VM**

Make sure your MySQL server is up and running on the VM. You can check this by running:

```bash
sudo systemctl status mysql
```

### 2. **Configure MySQL for Remote Access**

1. **Edit the MySQL Configuration File:**
   Open the MySQL configuration file (`my.cnf` or `my.ini`, depending on your OS):

   ```bash
   sudo nano /etc/mysql/my.cnf
   ```

   Look for the line:

   ```ini
   bind-address = 127.0.0.1
   ```

   Change it to:

   ```ini
   bind-address = 0.0.0.0
   ```

   This allows MySQL to accept connections from any IP address.

2. **Restart MySQL Service:**

   ```bash
   sudo systemctl restart mysql
   ```

### 3. **Create a MySQL User for Remote Access**

Log into MySQL:

```bash
mysql -u root -p
```

Then create a user that can connect remotely:

```sql
CREATE USER 'your_user'@'%' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON your_database.* TO 'your_user'@'%';
FLUSH PRIVILEGES;
```

Replace `your_database` with the actual database name.

### 4. **Firewall Configuration**

Ensure that the firewall on the VM allows traffic on the MySQL port (default is 3306).

For example, if you’re using `ufw` on Ubuntu, you can allow MySQL with:

```bash
sudo ufw allow 3306/tcp
```

### 5. **Get the VM’s IP Address**

You can find the VM’s IP address by running:

```bash
hostname -I
```

### 6. **Connect Your Web App**

Now that your MySQL server is set up for remote connections, you can connect your web application. Here’s an example connection string for different programming languages:

- **Node.js (using MySQL package):**

  ```javascript
  const mysql = require('mysql');

  const connection = mysql.createConnection({
    host: 'VM_IP_ADDRESS',
    user: 'your_user',
    password: 'your_password',
    database: 'your_database'
  });

  connection.connect(err => {
    if (err) throw err;
    console.log('Connected!');
  });
  ```

- **PHP (using PDO):**

  ```php
  try {
      $pdo = new PDO('mysql:host=VM_IP_ADDRESS;dbname=your_database', 'your_user', 'your_password');
      echo "Connected successfully";
  } catch (PDOException $e) {
      echo "Connection failed: " . $e->getMessage();
  }
  ```

- **Python (using MySQL Connector):**

  ```python
  import mysql.connector

  conn = mysql.connector.connect(
      host='VM_IP_ADDRESS',
      user='your_user',
      password='your_password',
      database='your_database'
  )

  print("Connected successfully")
  ```

### 7. **Testing the Connection**

Run your web app and check if it connects successfully to the MySQL database. Ensure there are no typos in your connection parameters.

### Troubleshooting

- **Check MySQL logs** for any errors related to connections.
- **Verify firewall settings** to ensure that the port is open.
- **Use tools like `telnet`** or `nc` to test if you can reach the MySQL port from your local machine.

By following these steps, you should be able to connect your web app to the MySQL database on the VM successfully! Let me know if you need more specific guidance based on your tech stack.