using Microsoft.IdentityModel.Tokens;
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;

namespace Platform.Api.Endpoints;

public static class AuthEndpoints
{
    public static void MapAuthEndpoints(this WebApplication app)
    {
        app.MapPost("/auth/login", () =>
        {
            var jwtSecret = app.Configuration["Jwt:SecretKey"];
            if (string.IsNullOrEmpty(jwtSecret))
            {
                throw new InvalidOperationException("JWT Secret Key is not configured.");
            }

            var keyBytes = Encoding.UTF8.GetBytes(jwtSecret);
            if (keyBytes.Length < 32)
            {
                throw new InvalidOperationException("JWT Secret Key must be at least 256 bits (32 bytes).");
            }

            var claims = new[]
            {
                new Claim(ClaimTypes.NameIdentifier, "1"),
                new Claim(ClaimTypes.Email, "user@test.com"),
                new Claim("tenant_id", "10")
            };

            var key = new SymmetricSecurityKey(keyBytes);

            var token = new JwtSecurityToken(
                claims: claims,
                expires: DateTime.UtcNow.AddHours(1),
                signingCredentials: new SigningCredentials(
                    key,
                    SecurityAlgorithms.HmacSha256
                )
            );

            try
            {
                var jwt = new JwtSecurityTokenHandler().WriteToken(token);
                return Results.Ok(new { token = jwt });
            }
            catch (Exception ex)
            {
                return Results.BadRequest(new { error = "Failed to generate JWT token.", details = ex.Message });
            }
        })
        .WithName("Login")
        .WithTags("Auth");
    }
}
