#!/bin/bash

# Function to wait for a specified amount of time
wait_for() {
    seconds=$1
    sleep $seconds
}

# Execute commands within the myst cli context
{
    # Start myst cli and wait for it to be ready
    echo "myst cli"
    wait_for 10

    # Create a new identity and capture the output
    new_identity_output=$(echo "identities new" | myst cli)
    wait_for 3

    # Extract the new identity hash from the output
    new_identity_hash=$(echo "$new_identity_output" | grep -oP '0x[a-fA-F0-9]{40}')

    # If no new identity hash was captured, exit the script with an error
    if [ -z "$new_identity_hash" ]; then
        echo "Error: Failed to capture new identity hash."
        exit 1
    fi

    # List identities to confirm the new identity is created
    echo "identities list"
    wait_for 3

    # Configure the MMN API key
    echo "mmn hqj4rJxeTQAndaUhnwH9WJDzFjXO4IIGK461EAxf"
    wait_for 3

    # Unlock the new identity using the captured hash
    echo "identities unlock $new_identity_hash"
    wait_for 3

    # Register the new identity using the captured hash
    echo "identities register $new_identity_hash"
    wait_for 3

    # Set the payout address for the new identity
    echo "identities set-payout-address 0x003b16A599707E4452E6fD30C54f31e1D518279C $new_identity_hash"
    wait_for 3

    # Start the service with the new identity using WireGuard
    echo "service start $new_identity_hash wireguard"
    wait_for 3

    # Exit myst cli
    echo "exit"
} | myst cli
