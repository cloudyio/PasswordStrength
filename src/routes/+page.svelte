<script lang="ts">
  import * as Card from "$lib/components/ui/card";
  import { Input } from "$lib/components/ui/input/index.js";
  import { Label } from "$lib/components/ui/label/index.js";
  import { Progress } from "$lib/components/ui/progress/index.js";
  const commonPasswords = ["123456", "password", "qwerty", "letmein", "abc123", 'password1'];
  let showPassword = false;
  let points = 0;
  let password = "";
  let strengthMessage = "This password is weak";
  let strengthColor = "text-red-700";
  let showTips = false;
  let tips = [];

  $: {
    strengthMessage = getStrengthMessage(points);
    strengthColor = getStrengthColor(points);
    tips = getTips(password);
  }

  function checkPoints(password: string) {
    points = 0;

    if (password.length >= 8 && password.length <= 10) {
      points += 1;
    } else if (password.length > 10 && password.length <= 13) {
      points += 2;
    } else if (password.length > 13) {
      points += 3;
    }

    if (/[a-z]/.test(password)) {
      points += 1;
    }

    if (/[A-Z]/.test(password)) {
      points += 1;
    }

    if (/[0-9]/.test(password)) {
      points += 1;
    }

    if (/[!@#$%^&*()_+\-=\[\]{};':"\\|,.<>\/?]/.test(password)) {
      points += 1;
    }

    if (/(.)\1{2,}/.test(password)) {
      points -= 1;
    }

    if (/123|234|345|456|567|678|789|890|abc|bcd|cde|def|.../.test(password)) {
      points -= 1;
    }

    if (commonPasswords.includes(password)) {
      points = 1;
    }

    console.log(points);
  }

  function getStrengthMessage(points: number): string {
    if (points <= 2) {
      return "This password is very weak";
    } else if (points <= 4) {
      return "This password is weak";
    } else if (points <= 6) {
      return "This password is strong";
    } else {
      return "This password is very strong";
    }
  }

  function getStrengthColor(points: number): string {
    if (points <= 2) {
      return "text-red-700";
    } else if (points <= 4) {
      return "text-yellow-500";
    } else if (points <= 6) {
      return "text-green-500";
    } else {
      return "text-blue-500";
    }
  }

  function getTips(password: string): string[] {
    const tips = [];
    if (password.length < 8) {
      tips.push("Use at least 8 characters.");
    }
    if (!/[a-z]/.test(password)) {
      tips.push("Include lowercase letters.");
    }
    if (!/[A-Z]/.test(password)) {
      tips.push("Include uppercase letters.");
    }
    if (!/[0-9]/.test(password)) {
      tips.push("Include numbers.");
    }
    if (!/[!@#$%^&*()_+\-=\[\]{};':"\\|,.<>\/?]/.test(password)) {
      tips.push("Include special characters.");
    }
    if (commonPasswords.includes(password)) {
      tips.push("Avoid common passwords.");
    }
    return tips;
  }
</script>

<div class="flex justify-center items-center min-h-screen bg-gray-900">
  <Card.Root class="w-full max-w-md p-6 rounded-lg shadow-md bg-gray-800">
    <Card.Header class="mb-4">
      <Card.Title class="text-2xl font-semibold text-gray-200">Password Checker</Card.Title>
      <Card.Description class="text-gray-400">Check the strength of a password</Card.Description>
    </Card.Header>
    <Card.Content>
      <Label class="block mb-2 text-sm font-medium text-gray-300">Password</Label>
      <div class="relative mb-4">
        <Input bind:value={password} on:input={() => checkPoints(password)} type={showPassword ? "text" : "password"} placeholder="Password" class="w-full h-10 px-3 py-2 border rounded-md shadow-sm focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent pr-10 bg-gray-700 border-gray-600 text-gray-200" />
        <button type="button" class="absolute inset-y-0 right-0 pr-3 flex items-center" on:click={() => showPassword = !showPassword}>
          {#if showPassword}
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-500" viewBox="0 0 20 20" fill="currentColor">
              <path d="M10 3a7 7 0 00-7 7 7 7 0 0014 0 7 7 0 00-7-7zm0 12a5 5 0 110-10 5 5 0 010 10zm0-8a3 3 0 100 6 3 3 0 000-6z" />
            </svg>
          {:else}
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-500" viewBox="0 0 20 20" fill="currentColor">
              <path fill-rule="evenodd" d="M10 3a7 7 0 00-7 7 7 7 0 0014 0 7 7 0 00-7-7zm0 12a5 5 0 110-10 5 5 0 010 10zm-1-5a1 1 0 112 0 1 1 0 01-2 0z" clip-rule="evenodd" />
            </svg>
          {/if}
        </button>
      </div>
      <p class="{strengthColor} text-sm">{strengthMessage}</p>
      <div class="flex justify-center mt-4">
        <Progress value={points} max={7} class="w-full h-2 bg-gray-600 rounded-full">
          <div class="h-full bg-blue-500 rounded-full" style="width: {points / 7 * 100}%"></div>
        </Progress>
      </div>
      <button class="mt-4 px-4 py-2 bg-gray-700 text-gray-200 font-semibold rounded-md shadow-md hover:bg-gray-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50" on:click={() => showTips = !showTips}>
        {#if showTips} Hide Tips {:else} Show Tips {/if}
      </button>
      {#if showTips}
        <ul class="mt-4 text-gray-300">
          {#each tips as tip}
            <li>{tip}</li>
          {/each}
        </ul>
      {/if}
    </Card.Content>
    <Card.Footer class="mt-4">
      <p class="text-xs text-gray-400">Anything inputted is never stored or sent to any server</p>
    </Card.Footer>
  </Card.Root>
</div>