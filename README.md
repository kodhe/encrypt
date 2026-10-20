# Kodhe Encrypt - Modernized CI3 Encrypt Library

A modern encryption library for CodeIgniter 3 with OpenSSL support, replacing the deprecated `mcrypt` library.

## ⚠️ DEPRECATED NOTICE

This library is **DEPRECATED** and is kept only for backward compatibility. For new projects, use the official OpenSSL-based CodeIgniter 3 **Encryption** library.

## 🚀 Key Features

- ✅ **OpenSSL Support**: AES-256-CBC + HMAC-SHA256 for maximum security
- ✅ **Backward Compatible**: API identical to the legacy CI3 Encrypt library
- ✅ **Legacy Data Support**: Can read old (mcrypt) encrypted data
- ✅ **Tamper Proof**: Automatically detects tampered data
- ✅ **PSR-4 Compliant**: Modular, modern structure
- ✅ **Performance Optimized**: Key-derivation caching, batch operations
- ✅ **Migration Path**: The `migrate()` method moves old data to the new format

## 📦 Installation

```bash
composer require kodhe/encrypt
```

Or add it to `composer.json`:

```json
{
    "require": {
        "kodhe/encrypt": "^1.0"
    }
}
```

## 🔧 Configuration

Add this in `application/config/config.php`:

```php
$config['encryption_key'] = 'your-secret-key-min-32-chars-long';
```

**IMPORTANT**: Use a key of at least 32 characters for optimal security!

## 💡 Usage

### Traditional CI3 Style

```php
$this->load->library('encrypt');

// Encryption
$encrypted = $this->encrypt->encode('secret data');

// Decryption
$decrypted = $this->encrypt->decode($encrypted);

// With a custom key
$encrypted = $this->encrypt->encode('data', 'custom-key');
```

### Modern PSR-4 Style

```php
use Kodhe\Encrypt\Encrypt;

$encrypt = new Encrypt([
    'encryption_key' => 'your-secret-key-min-32-chars-long',
    'algorithm' => 'aes-256-cbc', // optional
]);

// Encryption
$token = $encrypt->encode('user_id_123');

// Decryption
$user_id = $encrypt->decode($token);

// Batch operations (faster for large amounts of data)
$encrypted_array = $encrypt->encode_many(['data1', 'data2', 'data3']);
$decrypted_array = $encrypt->decode_many($encrypted_array);
```

## 🔐 Security

### New Data Format (OpenSSL)
```
Base64(IV[16 bytes] + HMAC-SHA256[32 bytes] + Ciphertext)
```

- **IV (Initialization Vector)**: Random for every encryption
- **HMAC**: Prevents tampering/data manipulation
- **AES-256-CBC**: Industry standard for symmetric encryption

### Tamper Detection

```php
$data = $encrypt->decode($encrypted);

if ($data === false) {
    // Data corrupted, wrong key, or tampered!
    log_message('error', 'Decryption failed - data may have been tampered with');
}
```

## 🔄 Migrating from Legacy (mcrypt)

This library automatically detects and reads legacy CI3 data:

```php
// Automatically detects the legacy format and decodes it
$old_data = $this->encrypt->decode($legacy_encrypted);

// Manual migration to the new format
$new_encrypted = $this->encrypt->migrate($legacy_encrypted);

// Check whether the data is still in the legacy format
if ($this->encrypt->is_legacy_format($encrypted)) {
    // Perlu migrasi
    $this->encrypt->migrate($encrypted);
}
```

### Database Migration Script

```php
// Migrate all encrypted data in the database
$users = $this->db->get('users')->result();

foreach ($users as $user) {
    $migrated = $this->encrypt->migrate($user->encrypted_data);
    
    if ($migrated !== false) {
        $this->db->where('id', $user->id)
                 ->update('users', ['encrypted_data' => $migrated]);
    }
}
```

## 📚 API Reference

| Method | Description | Returns |
|--------|-----------|--------|
| `encode($string, $key = '')` | Encrypts a string | string (base64) |
| `decode($string, $key = '')` | Decrypts a string | string\|false |
| `set_cipher($cipher)` | Sets the cipher algorithm | self |
| `set_mode($mode)` | Sets the operating mode | self |
| `sha1($str)` | Generates a SHA1 hash | string |
| `set_key($key)` | Sets the encryption key | self |
| `is_legacy_format($string)` | Checks for the legacy format | bool |
| `migrate($old_encoded)` | Migrates to the new format | string\|false |
| `encode_many($strings, $key)` | Batch encryption | array |
| `decode_many($encoded, $key)` | Batch decryption | array |

## 🧪 Testing

```bash
composer test
```

## 📊 Comparison with Original CI3

| Feature | CI3 Encrypt (mcrypt) | Kodhe Encrypt (OpenSSL) |
|-------|---------------------|-------------------------|
| Engine | Mcrypt ❌ (removed PHP 7.2+) | OpenSSL ✅ |
| Integrity | ❌ None | ✅ HMAC-SHA256 |
| Tamper Proof | ❌ Silent corruption | ✅ Detect & reject |
| IV generation | Weak random | ✅ `random_bytes()` |
| Key derivation | Simple substr | ✅ HKDF/PBKDF2 |
| Legacy support | N/A | ✅ Auto-detect |
| Batch ops | ❌ | ✅ `encode_many()` |

## ⚠️ Security Best Practices

1. **Use Long Keys**: At least 32 characters
2. **Store Keys Securely**: Don't commit them to Git; use environment variables
3. **Rotate Keys Periodically**: Change the encryption key regularly
4. **HTTPS Always**: Transmit encrypted data only over HTTPS
5. **Migrate Old Data**: Move legacy data to the new format as soon as possible

## 📝 Changelog

### v2.0.0 (2024)
- ✅ Full migration from mcrypt to OpenSSL
- ✅ Added HMAC for data integrity
- ✅ Support for auto-detecting the legacy format
- ✅ Batch operations for performance
- ✅ PSR-4 autoloading

### v1.0.0 (Legacy)
- Original CI3 Encrypt library compatibility

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

## 🤝 Contributing

Contributions welcome! Please read our contributing guidelines before submitting PRs.

## 📞 Support

- Issues: https://github.com/yourname/ci3-encrypt/issues
- Documentation: https://github.com/yourname/ci3-encrypt/wiki

---

**Note**: This library targets CodeIgniter 3. For CodeIgniter 4, use the built-in Encryption service.
