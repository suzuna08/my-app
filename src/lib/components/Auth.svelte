<script lang="ts">
  import { signIn, signUp, signOut } from '$lib/supabase';
  import { user } from '$lib/stores/auth';

  let email = '';
  let password = '';
  let isSignUp = false;
  let error = '';
  let loading = false;
  let showDropdown = false;

  async function handleSubmit() {
    error = '';
    loading = true;

    try {
      if (isSignUp) {
        const { error: signUpError } = await signUp(email, password);
        if (signUpError) {
          error = signUpError.message;
        } else {
          error = ''; // Clear any previous error
          // Show success message for sign up
          alert('Check your email for a confirmation link!');
        }
      } else {
        const { error: signInError } = await signIn(email, password);
        if (signInError) {
          error = signInError.message;
        }
      }
    } catch (e: any) {
      error = e.message || 'An error occurred';
    }

    loading = false;
  }

  async function handleSignOut() {
    await signOut();
    showDropdown = false;
  }

  function toggleDropdown() {
    showDropdown = !showDropdown;
  }

  // Close dropdown when clicking outside
  function handleClickOutside(event: MouseEvent) {
    const target = event.target as HTMLElement;
    if (!target.closest('.user-menu')) {
      showDropdown = false;
    }
  }
</script>

<svelte:window on:click={handleClickOutside} />

<div class="auth-container">
  {#if $user}
    <div class="user-menu">
      <button class="user-button" on:click|stopPropagation={toggleDropdown}>
        <span class="user-avatar">👤</span>
        <span class="user-email">{$user.email}</span>
        <span class="dropdown-arrow">{showDropdown ? '▲' : '▼'}</span>
      </button>
      
      {#if showDropdown}
        <div class="dropdown-menu">
          <div class="dropdown-header">
            <span class="dropdown-email">{$user.email}</span>
          </div>
          <button class="dropdown-item sign-out" on:click={handleSignOut}>
            <span>🚪</span> Sign Out
          </button>
        </div>
      {/if}
    </div>
  {:else}
    <div class="auth-form-container">
      <button class="auth-toggle-btn" on:click|stopPropagation={() => showDropdown = !showDropdown}>
        {showDropdown ? '✕ Close' : '🔐 Sign In'}
      </button>
      
      {#if showDropdown}
        <div class="auth-dropdown">
          <h3>{isSignUp ? 'Create Account' : 'Sign In'}</h3>
          
          <form on:submit|preventDefault={handleSubmit}>
            <input
              type="email"
              bind:value={email}
              placeholder="Email"
              required
            />
            <input
              type="password"
              bind:value={password}
              placeholder="Password"
              minlength="6"
              required
            />
            
            {#if error}
              <p class="error">{error}</p>
            {/if}
            
            <button type="submit" class="submit-btn" disabled={loading}>
              {loading ? 'Loading...' : (isSignUp ? 'Sign Up' : 'Sign In')}
            </button>
          </form>
          
          <p class="switch-mode">
            {isSignUp ? 'Already have an account?' : "Don't have an account?"}
            <button type="button" class="link-btn" on:click={() => isSignUp = !isSignUp}>
              {isSignUp ? 'Sign In' : 'Sign Up'}
            </button>
          </p>
        </div>
      {/if}
    </div>
  {/if}
</div>

<style>
  .auth-container {
    position: relative;
  }

  .user-menu {
    position: relative;
  }

  .user-button {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    padding: 0.5rem 1rem;
    background: #4285f4;
    color: white;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    font-size: 0.875rem;
    transition: background 0.2s;
  }

  .user-button:hover {
    background: #3367d6;
  }

  .user-avatar {
    font-size: 1.1rem;
  }

  .user-email {
    max-width: 150px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .dropdown-arrow {
    font-size: 0.6rem;
    margin-left: 0.25rem;
  }

  .dropdown-menu {
    position: absolute;
    top: calc(100% + 0.5rem);
    right: 0;
    background: white;
    border-radius: 8px;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
    min-width: 200px;
    z-index: 1000;
    overflow: hidden;
  }

  .dropdown-header {
    padding: 1rem;
    background: #f8f9fa;
    border-bottom: 1px solid #e0e0e0;
  }

  .dropdown-email {
    font-size: 0.875rem;
    color: #666;
    word-break: break-all;
  }

  .dropdown-item {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    width: 100%;
    padding: 0.75rem 1rem;
    background: none;
    border: none;
    cursor: pointer;
    font-size: 0.875rem;
    text-align: left;
    transition: background 0.2s;
  }

  .dropdown-item:hover {
    background: #f0f0f0;
  }

  .dropdown-item.sign-out {
    color: #dc3545;
  }

  .auth-form-container {
    position: relative;
  }

  .auth-toggle-btn {
    padding: 0.5rem 1rem;
    background: #4285f4;
    color: white;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    font-size: 0.875rem;
    transition: background 0.2s;
  }

  .auth-toggle-btn:hover {
    background: #3367d6;
  }

  .auth-dropdown {
    position: absolute;
    top: calc(100% + 0.5rem);
    right: 0;
    background: white;
    border-radius: 12px;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
    padding: 1.5rem;
    min-width: 280px;
    z-index: 1000;
  }

  .auth-dropdown h3 {
    margin: 0 0 1rem 0;
    font-size: 1.25rem;
    color: #333;
  }

  .auth-dropdown form {
    display: flex;
    flex-direction: column;
    gap: 0.75rem;
  }

  .auth-dropdown input {
    padding: 0.75rem 1rem;
    border: 1px solid #ddd;
    border-radius: 8px;
    font-size: 0.875rem;
    transition: border-color 0.2s;
  }

  .auth-dropdown input:focus {
    outline: none;
    border-color: #4285f4;
  }

  .error {
    color: #dc3545;
    font-size: 0.875rem;
    margin: 0;
    padding: 0.5rem;
    background: #fff5f5;
    border-radius: 4px;
  }

  .submit-btn {
    padding: 0.75rem 1rem;
    background: #4285f4;
    color: white;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    font-size: 0.875rem;
    font-weight: 500;
    transition: background 0.2s;
  }

  .submit-btn:hover:not(:disabled) {
    background: #3367d6;
  }

  .submit-btn:disabled {
    background: #ccc;
    cursor: not-allowed;
  }

  .switch-mode {
    margin: 1rem 0 0 0;
    font-size: 0.875rem;
    color: #666;
    text-align: center;
  }

  .link-btn {
    background: none;
    border: none;
    color: #4285f4;
    cursor: pointer;
    font-size: 0.875rem;
    text-decoration: underline;
  }

  .link-btn:hover {
    color: #3367d6;
  }

  @media (max-width: 768px) {
    .user-email {
      display: none;
    }

    .auth-dropdown {
      position: fixed;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      right: auto;
      width: calc(100vw - 2rem);
      max-width: 320px;
    }

    .dropdown-menu {
      position: fixed;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      right: auto;
      width: calc(100vw - 2rem);
      max-width: 280px;
    }
  }
</style>
