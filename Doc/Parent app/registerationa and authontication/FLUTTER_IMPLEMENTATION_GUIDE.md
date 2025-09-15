# SafeGuard Parent App - Flutter Implementation Guide

## Project Structure

```
lib/
├── main.dart
├── app/
│   ├── app.dart
│   └── routes.dart
├── core/
│   ├── constants/
│   │   ├── api_constants.dart
│   │   └── app_constants.dart
│   ├── errors/
│   │   ├── exceptions.dart
│   │   └── failures.dart
│   ├── network/
│   │   ├── api_client.dart
│   │   └── network_info.dart
│   └── utils/
│       ├── validators.dart
│       └── password_strength.dart
├── features/
│   └── authentication/
│       ├── data/
│       │   ├── datasources/
│       │   │   ├── auth_local_datasource.dart
│       │   │   └── auth_remote_datasource.dart
│       │   ├── models/
│       │   │   ├── user_model.dart
│       │   │   ├── auth_response_model.dart
│       │   │   └── login_request_model.dart
│       │   └── repositories/
│       │       └── auth_repository_impl.dart
│       ├── domain/
│       │   ├── entities/
│       │   │   ├── user.dart
│       │   │   └── auth_response.dart
│       │   ├── repositories/
│       │   │   └── auth_repository.dart
│       │   └── usecases/
│       │       ├── login_usecase.dart
│       │       ├── register_usecase.dart
│       │       ├── forgot_password_usecase.dart
│       │       └── reset_password_usecase.dart
│       └── presentation/
│           ├── pages/
│           │   ├── login_page.dart
│           │   ├── register_page.dart
│           │   ├── forgot_password_page.dart
│           │   └── reset_password_page.dart
│           ├── widgets/
│           │   ├── custom_text_field.dart
│           │   ├── password_field.dart
│           │   └── loading_button.dart
│           └── providers/
│               └── auth_provider.dart
└── shared/
    ├── widgets/
    │   ├── custom_app_bar.dart
    │   └── error_snackbar.dart
    └── utils/
        ├── theme.dart
        └── extensions.dart
```

## Dependencies (pubspec.yaml)

```yaml
dependencies:
  flutter:
    sdk: flutter
  
  # State Management
  provider: ^6.0.5
  riverpod: ^2.4.0
  flutter_riverpod: ^2.4.0
  
  # HTTP Client
  dio: ^5.3.2
  retrofit: ^4.0.3
  json_annotation: ^4.8.1
  
  # Local Storage
  shared_preferences: ^2.2.2
  secure_storage: ^9.0.0
  
  # Form Validation
  form_builder_validators: ^9.1.0
  flutter_form_builder: ^9.1.1
  
  # UI Components
  flutter_screenutil: ^5.9.0
  google_fonts: ^6.1.0
  lottie: ^2.7.0
  
  # Utils
  intl: ^0.18.1
  equatable: ^2.0.5
  dartz: ^0.10.1

dev_dependencies:
  flutter_test:
    sdk: flutter
  
  # Code Generation
  build_runner: ^2.4.7
  json_serializable: ^6.7.1
  retrofit_generator: ^8.0.4
  
  # Testing
  mockito: ^5.4.2
  bloc_test: ^9.1.5
```

## Core Implementation Files

### 1. API Client (lib/core/network/api_client.dart)

```dart
import 'package:dio/dio.dart';
import 'package:retrofit/retrofit.dart';
import 'package:json_annotation/json_annotation.dart';

part 'api_client.g.dart';

@RestApi(baseUrl: "https://your-api-domain.com/api")
abstract class ApiClient {
  factory ApiClient(Dio dio, {String baseUrl}) = _ApiClient;

  @POST("/auth/register")
  Future<AuthResponseModel> register(@Body() RegisterRequestModel request);

  @POST("/auth/login")
  Future<AuthResponseModel> login(@Body() LoginRequestModel request);

  @POST("/auth/forgot-password")
  Future<MessageResponseModel> forgotPassword(@Body() ForgotPasswordRequestModel request);

  @POST("/auth/reset-password")
  Future<MessageResponseModel> resetPassword(@Body() ResetPasswordRequestModel request);

  @POST("/auth/refresh")
  Future<TokenResponseModel> refreshToken(@Body() RefreshTokenRequestModel request);

  @POST("/auth/logout")
  Future<MessageResponseModel> logout();

  @GET("/auth/profile")
  Future<UserProfileModel> getProfile();

  @PUT("/auth/profile")
  Future<UserProfileModel> updateProfile(@Body() UpdateProfileRequestModel request);
}
```

### 2. Auth Provider (lib/features/authentication/presentation/providers/auth_provider.dart)

```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:shared_preferences/shared_preferences.dart';
import '../domain/usecases/login_usecase.dart';
import '../domain/usecases/register_usecase.dart';
import '../domain/usecases/forgot_password_usecase.dart';
import '../domain/usecases/reset_password_usecase.dart';

class AuthNotifier extends StateNotifier<AuthState> {
  final LoginUsecase _loginUsecase;
  final RegisterUsecase _registerUsecase;
  final ForgotPasswordUsecase _forgotPasswordUsecase;
  final ResetPasswordUsecase _resetPasswordUsecase;

  AuthNotifier(
    this._loginUsecase,
    this._registerUsecase,
    this._forgotPasswordUsecase,
    this._resetPasswordUsecase,
  ) : super(AuthInitial());

  Future<void> login(String email, String password, bool rememberMe) async {
    state = AuthLoading();
    
    final result = await _loginUsecase(LoginParams(
      email: email,
      password: password,
      rememberMe: rememberMe,
    ));

    result.fold(
      (failure) => state = AuthError(failure.message),
      (authResponse) {
        state = AuthAuthenticated(authResponse.user);
        _saveToken(authResponse.token);
      },
    );
  }

  Future<void> register(RegisterParams params) async {
    state = AuthLoading();
    
    final result = await _registerUsecase(params);

    result.fold(
      (failure) => state = AuthError(failure.message),
      (authResponse) {
        state = AuthAuthenticated(authResponse.user);
        _saveToken(authResponse.token);
      },
    );
  }

  Future<void> forgotPassword(String email) async {
    state = AuthLoading();
    
    final result = await _forgotPasswordUsecase(ForgotPasswordParams(email: email));

    result.fold(
      (failure) => state = AuthError(failure.message),
      (message) => state = AuthMessage(message),
    );
  }

  Future<void> resetPassword(String token, String newPassword) async {
    state = AuthLoading();
    
    final result = await _resetPasswordUsecase(ResetPasswordParams(
      token: token,
      newPassword: newPassword,
    ));

    result.fold(
      (failure) => state = AuthError(failure.message),
      (message) => state = AuthMessage(message),
    );
  }

  Future<void> logout() async {
    state = AuthLoading();
    
    final result = await _logoutUsecase();
    
    result.fold(
      (failure) => state = AuthError(failure.message),
      (_) {
        state = AuthUnauthenticated();
        _clearToken();
      },
    );
  }

  Future<void> _saveToken(String token) async {
    final prefs = await SharedPreferences.getInstance();
    await prefs.setString('auth_token', token);
  }

  Future<void> _clearToken() async {
    final prefs = await SharedPreferences.getInstance();
    await prefs.remove('auth_token');
  }
}

// State classes
abstract class AuthState {}

class AuthInitial extends AuthState {}

class AuthLoading extends AuthState {}

class AuthAuthenticated extends AuthState {
  final User user;
  AuthAuthenticated(this.user);
}

class AuthUnauthenticated extends AuthState {}

class AuthError extends AuthState {
  final String message;
  AuthError(this.message);
}

class AuthMessage extends AuthState {
  final String message;
  AuthMessage(this.message);
}

// Provider
final authProvider = StateNotifierProvider<AuthNotifier, AuthState>((ref) {
  return AuthNotifier(
    ref.read(loginUsecaseProvider),
    ref.read(registerUsecaseProvider),
    ref.read(forgotPasswordUsecaseProvider),
    ref.read(resetPasswordUsecaseProvider),
  );
});
```

### 3. Login Page (lib/features/authentication/presentation/pages/login_page.dart)

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:flutter_form_builder/flutter_form_builder.dart';
import 'package:form_builder_validators/form_builder_validators.dart';
import '../providers/auth_provider.dart';
import '../widgets/custom_text_field.dart';
import '../widgets/password_field.dart';
import '../widgets/loading_button.dart';

class LoginPage extends ConsumerStatefulWidget {
  const LoginPage({Key? key}) : super(key: key);

  @override
  ConsumerState<LoginPage> createState() => _LoginPageState();
}

class _LoginPageState extends ConsumerState<LoginPage> {
  final _formKey = GlobalKey<FormBuilderState>();
  bool _rememberMe = false;
  bool _isPasswordVisible = false;
  String _loginType = 'email';

  @override
  Widget build(BuildContext context) {
    final authState = ref.watch(authProvider);

    ref.listen<AuthState>(authProvider, (previous, next) {
      if (next is AuthError) {
        ScaffoldMessenger.of(context).showSnackBar(
          SnackBar(content: Text(next.message)),
        );
      } else if (next is AuthAuthenticated) {
        Navigator.pushReplacementNamed(context, '/dashboard');
      }
    });

    return Scaffold(
      body: SafeArea(
        child: Padding(
          padding: const EdgeInsets.all(24.0),
          child: FormBuilder(
            key: _formKey,
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: [
                const SizedBox(height: 40),
                const Text(
                  'Welcome Back',
                  style: TextStyle(
                    fontSize: 32,
                    fontWeight: FontWeight.bold,
                  ),
                  textAlign: TextAlign.center,
                ),
                const SizedBox(height: 8),
                const Text(
                  'Sign in to your SafeGuard account',
                  style: TextStyle(
                    fontSize: 16,
                    color: Colors.grey,
                  ),
                  textAlign: TextAlign.center,
                ),
                const SizedBox(height: 40),
                
                // Login Type Toggle
                Container(
                  decoration: BoxDecoration(
                    color: Colors.grey[100],
                    borderRadius: BorderRadius.circular(12),
                  ),
                  child: Row(
                    children: [
                      Expanded(
                        child: GestureDetector(
                          onTap: () => setState(() => _loginType = 'email'),
                          child: Container(
                            padding: const EdgeInsets.symmetric(vertical: 12),
                            decoration: BoxDecoration(
                              color: _loginType == 'email' ? Colors.white : Colors.transparent,
                              borderRadius: BorderRadius.circular(12),
                              boxShadow: _loginType == 'email' ? [
                                BoxShadow(
                                  color: Colors.black.withOpacity(0.1),
                                  blurRadius: 4,
                                  offset: const Offset(0, 2),
                                ),
                              ] : null,
                            ),
                            child: const Text(
                              'Email',
                              textAlign: TextAlign.center,
                              style: TextStyle(fontWeight: FontWeight.w500),
                            ),
                          ),
                        ),
                      ),
                      Expanded(
                        child: GestureDetector(
                          onTap: () => setState(() => _loginType = 'phone'),
                          child: Container(
                            padding: const EdgeInsets.symmetric(vertical: 12),
                            decoration: BoxDecoration(
                              color: _loginType == 'phone' ? Colors.white : Colors.transparent,
                              borderRadius: BorderRadius.circular(12),
                              boxShadow: _loginType == 'phone' ? [
                                BoxShadow(
                                  color: Colors.black.withOpacity(0.1),
                                  blurRadius: 4,
                                  offset: const Offset(0, 2),
                                ),
                              ] : null,
                            ),
                            child: const Text(
                              'Phone',
                              textAlign: TextAlign.center,
                              style: TextStyle(fontWeight: FontWeight.w500),
                            ),
                          ),
                        ),
                      ),
                    ],
                  ),
                ),
                
                const SizedBox(height: 24),
                
                // Email/Phone Field
                CustomTextField(
                  name: _loginType == 'email' ? 'email' : 'phone',
                  label: _loginType == 'email' ? 'Email Address' : 'Phone Number',
                  hint: _loginType == 'email' ? 'Enter your email' : 'Enter your phone',
                  keyboardType: _loginType == 'email' ? TextInputType.emailAddress : TextInputType.phone,
                  validators: [
                    FormBuilderValidators.required(),
                    if (_loginType == 'email') FormBuilderValidators.email(),
                  ],
                ),
                
                const SizedBox(height: 16),
                
                // Password Field
                PasswordField(
                  name: 'password',
                  label: 'Password',
                  hint: 'Enter your password',
                  isVisible: _isPasswordVisible,
                  onToggleVisibility: () => setState(() => _isPasswordVisible = !_isPasswordVisible),
                ),
                
                const SizedBox(height: 16),
                
                // Remember Me & Forgot Password
                Row(
                  mainAxisAlignment: MainAxisAlignment.spaceBetween,
                  children: [
                    Row(
                      children: [
                        Checkbox(
                          value: _rememberMe,
                          onChanged: (value) => setState(() => _rememberMe = value ?? false),
                        ),
                        const Text('Remember me'),
                      ],
                    ),
                    TextButton(
                      onPressed: () => Navigator.pushNamed(context, '/forgot-password'),
                      child: const Text('Forgot Password?'),
                    ),
                  ],
                ),
                
                const SizedBox(height: 24),
                
                // Login Button
                LoadingButton(
                  onPressed: _handleLogin,
                  isLoading: authState is AuthLoading,
                  text: 'Sign In',
                ),
                
                const SizedBox(height: 24),
                
                // Sign Up Link
                Row(
                  mainAxisAlignment: MainAxisAlignment.center,
                  children: [
                    const Text("Don't have an account? "),
                    TextButton(
                      onPressed: () => Navigator.pushNamed(context, '/register'),
                      child: const Text('Create account'),
                    ),
                  ],
                ),
              ],
            ),
          ),
        ),
      ),
    );
  }

  void _handleLogin() {
    if (_formKey.currentState?.saveAndValidate() ?? false) {
      final formData = _formKey.currentState!.value;
      
      ref.read(authProvider.notifier).login(
        formData[_loginType] ?? '',
        formData['password'] ?? '',
        _rememberMe,
      );
    }
  }
}
```

### 4. Password Field Widget (lib/features/authentication/presentation/widgets/password_field.dart)

```dart
import 'package:flutter/material.dart';
import 'package:flutter_form_builder/flutter_form_builder.dart';
import 'package:form_builder_validators/form_builder_validators.dart';

class PasswordField extends StatelessWidget {
  final String name;
  final String label;
  final String hint;
  final bool isVisible;
  final VoidCallback onToggleVisibility;
  final List<FormFieldValidator<String>>? validators;

  const PasswordField({
    Key? key,
    required this.name,
    required this.label,
    required this.hint,
    required this.isVisible,
    required this.onToggleVisibility,
    this.validators,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return FormBuilderTextField(
      name: name,
      obscureText: !isVisible,
      decoration: InputDecoration(
        labelText: label,
        hintText: hint,
        prefixIcon: const Icon(Icons.lock_outline),
        suffixIcon: IconButton(
          icon: Icon(isVisible ? Icons.visibility_off : Icons.visibility),
          onPressed: onToggleVisibility,
        ),
        border: OutlineInputBorder(
          borderRadius: BorderRadius.circular(12),
        ),
        filled: true,
        fillColor: Colors.grey[50],
      ),
      validators: validators ?? [
        FormBuilderValidators.required(),
        FormBuilderValidators.minLength(8),
      ],
    );
  }
}
```

### 5. Loading Button Widget (lib/features/authentication/presentation/widgets/loading_button.dart)

```dart
import 'package:flutter/material.dart';

class LoadingButton extends StatelessWidget {
  final VoidCallback? onPressed;
  final bool isLoading;
  final String text;
  final Color? backgroundColor;
  final Color? textColor;

  const LoadingButton({
    Key? key,
    required this.onPressed,
    required this.isLoading,
    required this.text,
    this.backgroundColor,
    this.textColor,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return SizedBox(
      height: 56,
      child: ElevatedButton(
        onPressed: isLoading ? null : onPressed,
        style: ElevatedButton.styleFrom(
          backgroundColor: backgroundColor ?? Theme.of(context).primaryColor,
          foregroundColor: textColor ?? Colors.white,
          shape: RoundedRectangleBorder(
            borderRadius: BorderRadius.circular(12),
          ),
          elevation: 2,
        ),
        child: isLoading
            ? const SizedBox(
                height: 20,
                width: 20,
                child: CircularProgressIndicator(
                  strokeWidth: 2,
                  valueColor: AlwaysStoppedAnimation<Color>(Colors.white),
                ),
              )
            : Text(
                text,
                style: const TextStyle(
                  fontSize: 16,
                  fontWeight: FontWeight.w600,
                ),
              ),
      ),
    );
  }
}
```

## Key Features Implementation

### 1. Form Validation
- Real-time validation using FormBuilder
- Custom validators for email, phone, and password strength
- Visual feedback for validation errors

### 2. State Management
- Riverpod for reactive state management
- Provider pattern for dependency injection
- Clean separation of concerns

### 3. API Integration
- Dio for HTTP requests
- Retrofit for type-safe API calls
- Automatic JSON serialization/deserialization

### 4. Local Storage
- SharedPreferences for token storage
- Secure storage for sensitive data
- Automatic token refresh

### 5. Error Handling
- Global error handling
- User-friendly error messages
- Network error handling

### 6. UI/UX
- Material Design 3
- Responsive design
- Loading states and animations
- Form validation feedback

This implementation provides a solid foundation for the SafeGuard Parent App authentication system using Flutter and follows best practices for maintainable, scalable mobile application development.
