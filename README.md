
# NestJs Authentication & Authorization With passportjs & Claim-Based Access

Are you tired of worrying about user authentication and authorization in your NestJS applications and want claim-based authorization? Look no further! This project provides a comprehensive, battle-tested solution for managing user access and permissions, so you can focus on building amazing apps.

Built for developers, by developers

Whether you're a solo dev or part of a team, this project is designed to help you:

1. Secure your app with robust authentication and authorization

2. Scale your solution with confidence

3. Customize access control to fit your unique needs


## API Reference

#### Create a User

```http
  POST  http://localhost:3000/users/signup
```

| Parameter | Type     | Description                |
| :-------- | :------- | :------------------------- |
| `username,password,email` | `string` | **Required**. Running database |

#### Login User

```http
  POST  http://localhost:3000/auth/login
```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `username,password`      | `string` | **Required**.  |

#### View  Profile

```http
  GET http://localhost:3000/profile
```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `username,password`      | `string` | **Required**. Jwt Access Token |

#### View Protected Route (Based On Role based Access)

```http
  POST http://localhost:3000/profile
```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `username,password`      | `string` | **Required**. Jwt Bearer Token |


#### Create an Admin

```http
  POST  http://localhost:3000/admin/signup
```

| Parameter | Type     | Description                |
| :-------- | :------- | :------------------------- |
| `username,password,email` | `string` | **Required**. Running database |

#### Login Admin

```http
  POST   http://localhost:3000/auth/login
```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `username,password`      | `string` | **Required**.  |

#### View  Profile

```http
  GET http://localhost:3000/profile
```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `username,password`      | `string` | **Required**. Jwt Access Token |

#### View Protected Route (Based On Role based Access)

```http
  POST http://localhost:3000/profile
```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `username,password`      | `string` | **Required**. Jwt Bearer Token |

#### View admin only Resource (Based On Claim based Access)

```http
  GET http://localhost:3000/admin-only
```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `username,password`      | `string` | **Required**. Jwt Bearer Token |

#### View user only Resource (Based On Claim based Access)

```http
  GET http://localhost:3000/user-only
```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `username,password`      | `string` | **Required**. Jwt Bearer Token |



## Appendix

Any additional information goes here


## Author

- [AtiqBytes](https://github.com/AtiqBytes)
## Badges

Add badges from somewhere like: [shields.io](https://shields.io/)

[![MIT License](https://img.shields.io/badge/License-MIT-green.svg)](https://choosealicense.com/licenses/mit/)
[![GPLv3 License](https://img.shields.io/badge/License-GPL%20v3-yellow.svg)](https://opensource.org/licenses/)
[![AGPL License](https://img.shields.io/badge/license-AGPL-blue.svg)](http://www.gnu.org/licenses/agpl-3.0)


## Contributing

Contributions are always welcome!

See `contributing.md` for ways to get started.

Please adhere to this project's `code of conduct`.


## Demo



## Deployment

To deploy this project run

```bash
  npm run start:dev
```


## Documentation

1.  [Jwt](https://jwt.io/)

2.  [Passport Js](https://docs.nestjs.com/recipes/passport)


## Environment Variables

To run this project, you will need to add the following environment variables to your .env file. You can customize it the way you want :

`DB_PORT='5432'`

`DB_USERNAME='postgres'`

`DB_PASSWORD='root'`

`DB_DATABASE='passportdb'`


# FAQS

#### 1. Why are the permissions missing in my JWT token?

Issue: When you generate a JWT token, the permissions are not included in the token payload after decoding it. You only see username and iat.

Solution: This usually happens because of a typo or case sensitivity issue in your JWT payload creation. Ensure that you are passing the permissions correctly from your user object. In auth.service.ts, use user.permissions (with lowercase "p"), like this:

```typescript
const payload = { username: user.username, sub: user.userId, permissions: user.permissions };
```

Ensure that user.permissions is properly populated with the relevant permissions before signing the JWT.

#### 2. Why am I getting TypeError: Cannot read properties of undefined (reading 'includes') in PermissionsGuard?

Issue: When trying to access a route that requires permissions, you encounter a TypeScript error:
TypeError: Cannot read properties of undefined (reading 'includes').

Solution: This error occurs because the permissions field might be missing or undefined in the user object. Make sure the user object has permissions before trying to check them.

Check that user.permissions is being correctly set in the authentication process (e.g., in auth.service.ts).

Modify the guard to ensure that user.permissions exists before checking if it includes the required permission:

```typescript
const hasPermission = requiredPermissions.every(permission => user.permissions?.includes(permission));
```

#### 3. How do I fix TypeScript errors related to permissions in auth.service.ts?

Issue: You encounter errors like:
Property 'permissions' does not exist on type '{ id: number; username: string; email: string; role: string; isActive: boolean; }'.

Solution: This issue occurs because the TypeScript type for your user object does not include the permissions field. Create a DTO (Data Transfer Object) that explicitly includes the permissions field:

```typescript
export class UserWithPermissions {
  id: number;
  username: string;
  password?: string;
  email: string;
  role: string;
  isActive: boolean;
  permissions?: string[];  // Optional permissions field
}
```

Then, in your auth.service.ts, assert that the user object matches this type using:
```typescript
const { password, ...result } = user as UserWithPermissions;
```

#### 4. Why is the PermissionsGuard not recognizing my user’s permissions?

Issue: The PermissionsGuard is failing to verify the permissions for the user, and you see logs indicating that user.permissions is undefined.

Solution: Ensure that the permissions are included in the JWT token when it’s generated. In your auth.service.ts, verify that the permissions are properly added to the JWT payload:

```typescript
const payload = { username: user.username, sub: user.userId, permissions: user.permissions };
```

Also, double-check that permissions is correctly assigned in the authentication process, based on the user’s role, before the token is generated.

#### 5. How do I assign different permissions to admin and regular users?

Issue: You want to assign specific permissions to users based on their role (admin or user), but the permissions are not being applied correctly.

Solution: In your auth.service.ts, when validating the user, check the role and assign permissions accordingly:

```typescript
if (user.role === 'admin') {
  result.permissions = [
    Permission.GENERAL_ADMIN_PERMISSION,
    Permission.GENERAL_USER_PERMISSION,
    Permission.BLOCK_USER
  ];
} else if (user.role === 'user') {
  result.permissions = [Permission.GENERAL_USER_PERMISSION];
}
```

Ensure you also return the user object with the permissions field in the correct format.

#### 6. Why are routes not protected correctly by the PermissionsGuard?

Issue: Even after applying the PermissionsGuard, users without the required permissions can still access protected routes.

Solution: Make sure you’ve applied both the JwtAuthGuard and PermissionsGuard to the route. For example:

```typescript
@UseGuards(JwtAuthGuard, PermissionsGuard)
@Permissions(Permission.GENERAL_USER_PERMISSION)
@Get('user-only')
userOnly() {
  return 'This is a user-only route';
}
```

Also, ensure that the PermissionsGuard is correctly implemented, checking both required permissions and user permissions.

#### 7. Why are some routes requiring authentication even though they shouldn’t?

Issue: You’ve applied a global JwtAuthGuard, but some routes like signup or login should be publicly accessible without authentication.

Solution: To allow specific routes to be accessed without authentication, use a decorator that bypasses the global guard, like @Public():

```typescript
@Public()
@Post('signup')
async signup(@Body() createUserDto: CreateUserDto) {
  return this.authService.signup(createUserDto);
}
```

This ensures that routes like signup and login are accessible without a token while keeping other routes protected by the JwtAuthGuard.

#### 8. How do I troubleshoot permission issues in the PermissionsGuard?

Issue: You are unsure whether the correct permissions are being checked or why the user’s permissions are not being matched.

Solution: Add logging to the PermissionsGuard to help debug issues by printing both the required permissions and the user’s permissions:

```typescript
console.log('Required permissions:', requiredPermissions);
console.log('User permissions:', user.permissions);
```

This will allow you to see what permissions are being required for the route and what permissions are present in the user’s JWT.

#### 9. Why is bcrypt not validating passwords correctly?

Issue: Password validation fails even though the correct password is provided, leading to failed login attempts.

Solution: Ensure that you are using bcrypt.compare properly, and double-check the password hash stored in the database. The comparison should look like this:

```typescript
if (user && await bcrypt.compare(password, user.password)) {
  // Password matches
}
```

If validation still fails, verify that the password hashing and storage process (e.g., bcrypt.hash) is correctly implemented during user registration or password updates.









## Features

- Role-Based Access Control
- JSON Web Token Authentication
- Customizable Authentication Flow
- Environment Variable Configuration
- Database Support
- Error Handling and Logging
- Cross-Platform Compatibility

## Feedback

If you have any feedback, please reach out to me at https://www.linkedin.com/in/atiq-ur-rehman-1314712aa/


## 🚀 About Me
I'm a full stack developer...

wokring in Angular | 
NestJs | 
Django |
Amazon Aws
# Hi, I'm Atiq Ur Rehman! 👋


## 🔗 Links
[![portfolio](https://img.shields.io/badge/my_portfolio-000?style=for-the-badge&logo=ko-fi&logoColor=white)](https://github.com/AtiqBytes)
[![linkedin](https://img.shields.io/badge/linkedin-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/atiq-ur-rehman-1314712aa/)



## Other Common Github Profile Sections
👩‍💻 I'm currently working on Backend

🧠 I'm currently learning NestJs

👯‍♀️ I'm looking to collaborate on full Stack Projects

🤔 I'm looking for help with getting Internship



## 🛠 Skills
Typescript, Javascript, HTML, CSS, Python


## Installation

Install my-project with npm

```bash
  cd my-project
  npm install 
  
```
    

## Lessons Learned:

## Claim-Based Authorization with Permissions:

Files Involved: permissions.decorator.ts, permissions.guard.ts, auth.service.ts

- I learned how to implement claim-based authorization by creating a Permission enum in permissions.decorator.ts, which holds the different permission levels, such as GENERAL_ADMIN_PERMISSION, GENERAL_USER_PERMISSION, and BLOCK_USER.
- By utilizing the Permissions decorator, I could attach required permissions to routes, enforcing authorization checks before granting access.
- The PermissionsGuard was created to intercept requests and check if the user had the necessary permissions to access specific routes.
- This involved extracting permissions from the user's JWT and matching them against the required permissions.
  
## Issues with Missing Permissions in JWT Payload:

Files Involved: auth.service.ts

- Initially, when I generated the JWT token in the auth.service.ts, I encountered an issue where the permissions were missing from the token payload after decoding it.
-  Instead, the payload only contained the username and iat claims.
-  The issue was due to using user.Permissions with an uppercase "P" instead of user.permissions (lowercase), which led to the permissions not being included in the JWT token.
-  To fix this, I updated the generateAccessToken and generateRefreshToken functions to use user.permissions and ensured the permissions were properly added to the JWT payload.
  
## PermissionsGuard Not Recognizing User Permissions:

Files Involved: permissions.guard.ts

- While testing the authorization with Postman, I encountered an error when trying to access routes with user tokens. The error message was:
  
```typescript
TypeError: Cannot read properties of undefined (reading 'include')
```

- The problem arose because the PermissionsGuard was trying to access user.Permissions with an uppercase "P", but the user object had the permissions field in lowercase (user.permissions).

- I also realized that user.permissions could be undefined in some cases, so I added a check to ensure that the permissions array exists before using .includes().
- After handling this, the guard correctly verified if the user had the required permissions.

## TypeScript Type Errors with Permissions in Auth Service:

Files Involved: auth.service.ts, userWithPermission.dto.ts

- When defining the validateUser function, I used Promise<UserWithPermissions | null>, which specifies that the function can return a user object with permissions or null.
- Initially, I encountered a TypeScript error when trying to assign permissions to the result.permissions field:

```typescript
Property 'permissions' does not exist on type '{ id: number; username: string; email: string; role: string; isActive: boolean; }'.ts(2339)
```

- To resolve this, I created a new DTO (userWithPermission.dto.ts) called UserWithPermissions, which extended the user entity to include the permissions field.
- By asserting the type of user as UserWithPermissions, I ensured that TypeScript recognized the permissions field, fixing the error.

## Token Payload and Permissions Encoding:

Files Involved: auth.service.ts

- Another issue I faced was that after logging in, the JWT token generated didn’t include the user’s permissions. This caused protected routes to fail when trying to check for permissions-based access.
- I resolved this by ensuring the correct permissions were included in the JWT payload by using user.permissions in both the access and refresh token generation functions.
  
## PermissionsGuard Logging and Debugging:

Files Involved: permissions.guard.ts

- To troubleshoot the permission issues, I added logging in permissions.guard.ts to print both the required permissions and the user’s permissions from the request.
-  This helped me identify that user.permissions was undefined in some cases.
- After fixing the case sensitivity and ensuring permissions were included in the JWT, I saw that the permissions were properly logged and validated against the required permissions for the route.
  
## Role and Permissions for Admin Users:

Files Involved: auth.service.ts

- While validating users in the auth.service.ts, I needed to assign different permissions based on the user’s role (admin or regular user). Initially, the permissions for admins were not correctly applied.
- I used the UserWithPermissions DTO to define a structure for users and added a condition that if a user had the role of "admin," they would receive GENERAL_ADMIN_PERMISSION, GENERAL_USER_PERMISSION, and BLOCK_USER permissions.
- This approach ensured that both regular users and admins were assigned the correct set of permissions when their credentials were validated.

  
  


## License

[MIT](https://choosealicense.com/licenses/mit/)


![Logo](https://docs.nestjs.com/assets/logo-small-gradient.svg)


## Related

Here are some related projects

[Awesome README](https://github.com/matiassingers/awesome-readme)


## Running Tests

To run tests, run the following command

```bash
  npm run test
```
unit tests : 
 ```
 
 npm run test
 ```

 e2e tests : 
```
$ npm run test:e2e
```
test coverage: 
```

 npm run test:cov
```

## Run Locally

Clone the project

```bash
  git clone https://github.com/AtiqBytes/passport-jwt-nestjs.git
```

Go to the project directory

```bash
  cd my-project
```

Install dependencies

```bash
  npm install
```

Start the server

```bash
  npm run start:dev
```

