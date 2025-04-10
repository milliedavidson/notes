# Insecure code examples

## Logging into an endpoint

### ⚠️ Insecure example 1

```csharp
// Client code
namespace InsecureEndpointLogin;

class Program
{
    static void Main(string[] args)
    {
        // Read credentials from a local file
        string[] credentials = File.ReadAllLines("credentials.txt");
        string username = credentials[0];
        string password = credentials[1];

        // Construct the login URL with direct string concatenation - VULNERABLE
        string apiBaseUrl = "https://api.example.com/login?";
        string loginUrl = apiBaseUrl + "username=" + username + "&password=" + password;
        // The credentials are added to the URL directly, so they are in plain view!

        // Make HTTP request to the endpoint
        using (HttpClient client = new HttpClient())
        {
            HttpResponseMessage response = await client.GetAsync(loginUrl);
            string responseContent = await response.Content.ReadAsStringAsync();
        }
    }
}
```

```csharp
// Server code
namespace InsecureApi.Controllers;

[ApiController]
[Route("[controller]")]
public class LoginController : ControllerBase
{
    private readonly string _connectionString = 
        "Server=myServerAddress;Database=myDatabase;Trusted_Connection=True;";
    
    [HttpGet] // GET request is vulnerable here
    public IActionResult Login(string username, string password)
    {
        // Create connection to the database
        using (SqlConnection connection = new SqlConnection(_connectionString))
        {
            connection.Open();

            // VULNERABLE! Because of direct string concatenation in the SQL query
            string query = 
                "SELECT * FROM Users WHERE Username = '" + username + "' AND Password = '" + password + "'";

            // Execute the query
            SqlCommand command = new SqlCommand(query, connection);
            SqlDataReader reader = command.ExecuteReader();
        }
    }
}
```

Using a **GET request** for logging in to an endpoint *exposes credentials* in the browser history, server logs, proxy logs and in the referrer headers.

If someone were to create a file called `credentials.txt` with the following content:

```plaintext
admin' OR '1'='1
anything
```

Then the URL would become:

```plaintext
https://api.example.com/login?username=admin' OR '1'='1&password=anything
```

And the server would execute:

```sql
SELECT * FROM Users WHERE Username = 'admin' OR '1'='1' AND Password = 'anything'
```

Since **'1'='1'** is a statement which always evaluates to true, this would authenticate as 'admin', allowing an attacker to authenticate as 'admin' without knowing the password!

### Secure example 1

To make this secure, use a POST request with a JSON body and also use parameterised queries. Ensure there is proper input validation and sanitisation:

```csharp
// Client code

// Validate inputs before sending
if (string.IsNullOrWhiteSpace(username) || username.Length > 50)
{
    Console.WriteLine("Error: Invalid username format");
    return;
}

if (string.IsNullOrWhiteSpace(password) || password.Length < 8)
{
    Console.WriteLine("Error: Password must be at least 8 characters");
    return;
}

username = SanitiseInput(username);
// Only sanitise the username, not the password, as it may contain special characters

var loginData = new { username = username, password = password };
var content = new StringContent(JsonConvert.SerializeObject(loginData), 
                               Encoding.UTF8, "application/json");

HttpResponseMessage response = await client.PostAsync("https://api.example.com/login", content);

// Helper method for input sanitisation
static string SanitiseInput(string input)
{
    if (string.IsNullOrEmpty(input)) return string.Empty;
    
    // Sanitise inputs (meaning, remove potentially dangerous characters)
    input = input.Replace("<", "&lt;").Replace(">", "&gt;");
    input = input.Replace("\"", "&quot;").Replace("'", "&#39;");
    
    // Additional sanitisation can be added based on requirements
    return input.Trim();
}
```

```csharp
// Server code

// Input validation
    if (model == null)
    {
        return BadRequest(new { message = "Invalid request format" });
    }
    
    // Validate username
    if (string.IsNullOrWhiteSpace(model.Username) || model.Username.Length > 50)
    {
        return BadRequest(new { message = "Invalid username format" });
    }
    
    // Validate password
    if (string.IsNullOrWhiteSpace(model.Password) || model.Password.Length < 8)
    {
        return BadRequest(new { message = "Invalid password format" });
    }

[HttpPost] // Post request is secure for logging in
public IActionResult Login([FromBody] LoginModel model)
{
    string query = "SELECT * FROM Users WHERE Username = @Username AND Password = @Password";
    // No concatenation here - query is using parameters i.e. it is parameterised

    SqlCommand command = new SqlCommand(query, connection);
    command.Parameters.AddWithValue("@Username", model.Username);
    command.Parameters.AddWithValue("@Password", model.Password);
}
```

## 

