---
title: "Repository"
date: 2018-06-08T14:55:37+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

## Repository classes

In the **Data** folder create a new **IAuthRepository** interface.

Add the following methods declarations to the interface

```c#
  Task<User> Register(User user, string password);
  Task<User> Login(string username, string password);
  Task<bool> UserExists(string username);
```

In the **Data** folder create a new **AuthRepository** class. Sepcify that the class implements **IAuthRepository** and use right click on the interface name to *Implement Interface*. Then mark all the methods with the **async** keyword.

Create a constructor that takes a DatingContext parameter and set it to a private field.

## Implementation

To register a user we need a private method to hash a plaintext password.

We use the __Hash Based Message Authentication Code__ class *System.Security.Cryptography.HMACSHA512*.

```c#
using(var hmac = new System.Security.Cryptography.HMACSHA512() )
{
  passwordSalt = hmac.Key;
  passwordHash = hmac.ComputeHash(System.Text.Encoding.UTF8.GetBytes(password));
}
```

To Login a user we need another private method to check that the password, passwordHash and passwordSalt match. Again we use **HMACSHA512**, then compare byte by byte the two hashes.

```c#
using (var hmac = new System.Security.Cryptography.HMACSHA512(passwordSalt))
{
    byte[] computedHash = hmac.ComputeHash(System.Text.Encoding.UTF8.GetBytes(password));

    if (passwordHash.Length != computedHash.Length) return false;

    for (int i = 0; i < computedHash.Length; i++)
    {
        if(passwordHash[i] != computedHash[i]) return false;
    }

    return true;
}
```

The implementation of the three methods of the interface is trivial.

```c#
using System;
using System.Threading.Tasks;
using DatingApp.API.Models;
using Microsoft.EntityFrameworkCore;

namespace DatingApp.API.Data
{
    public class AuthRepository : IAuthRepository
    {
        private readonly DatingContext _context;

        public AuthRepository(DatingContext context)
        {
            _context = context;
        }

        public async Task<User> Login(string username, string password)
        {
            var user = await _context.Users.FirstOrDefaultAsync(x => x.Username == username);

            bool isPasswordValid = CheckPasswordHash(password, user.PasswordHash, user.PasswordSalt);

            if (isPasswordValid)
            {
                return user;
            }
            else
            {
                return null;
            }
        }

        public async Task<User> Register(User user, string password)
        {
            // The password must be a not empty string
            if (string.IsNullOrWhiteSpace(password)) return null;

            (user.PasswordHash, user.PasswordSalt) = ComputeHashSalt(password);
            await _context.Users.AddAsync(user);
            await _context.SaveChangesAsync();

            return user;
        }

        public async Task<bool> UserExists(string username)
        {
            return await _context.Users.AnyAsync(x => x.Username == username);
        }

        private (byte[] passwordHash, byte[] passwordSalt) ComputeHashSalt(string password)
        {
            using (var hmac = new System.Security.Cryptography.HMACSHA512())
            {
                byte[] passwordSalt = hmac.Key;
                byte[] passwordHash = hmac.ComputeHash(System.Text.Encoding.UTF8.GetBytes(password));

                return (passwordHash: passwordHash, passwordSalt: passwordSalt);
            }
        }

        private bool CheckPasswordHash(string password, byte[] passwordHash, byte[] passwordSalt)
        {
            using (var hmac = new System.Security.Cryptography.HMACSHA512(passwordSalt))
            {
                byte[] computedHash = hmac.ComputeHash(System.Text.Encoding.UTF8.GetBytes(password));

                if (passwordHash.Length != computedHash.Length) return false;

                for (int i = 0; i < computedHash.Length; i++)
                {
                    if(passwordHash[i] != computedHash[i]) return false;
                }

                return true;
            }
        }
    }
}
```
