
*Step 1: Create a new branch*

```
bash
git checkout -b username/add-community-description
```

*Step 2: Update Community struct*

In `(link unavailable)` or your schema file, add an optional `description` field:

```
use anchor_lang::prelude::*;

#[account]
pub struct Community {
    // existing fields...
    pub description: Option<String>,
}
```

*Step 3: Update community_update instruction*

In your instruction handler, add logic to update the `description` field:

```
use anchor_lang::prelude::*;

pub fn community_update(ctx: Context<CommunityUpdate>, new_description: Option<String>) -> ProgramResult {
    let community = &mut ctx.accounts.community;
    community.description = new_description;
    Ok(())
}

#[derive(Accounts)]
pub struct CommunityUpdate<'info> {
    #[account(mut)]
    pub community: Account<'info, Community>,
    // existing accounts...
}
```

*Step 4: Test the behavior*

Create a test for the updated instruction:

```
use anchor_lang::prelude::*;

#[test]
fn test_community_update() {
    let mut community = Community {
        // existing fields...
        description: Some("Initial Description".to_string()),
    };
    let new_description = Some("Updated Description".to_string());

    let (mut banks_client, payer, recent_blockhash) = setup();

    let tx = Transaction::new_with_payer(
        &[
            Instruction::new_with_borsh(
                // program id...
                &community_update(
                    CommunityUpdate {
                        community: community.to_account_info(),
                        // existing accounts...
                    },
                    new_description,
                ),
                // existing accounts...
            ),
        ],
        Some(&payer.pubkey()),
    );

    banks_client.process_transaction(tx).unwrap();

    assert_eq!(community.description, new_description);
}
```

*Step 5: Update web UI components*

Update the respective components to display and edit the `description` field:

1. `PubkeyProtocolUiCommunityHeader`
2. `PubkeyProtocolUiCommunityGridItem`
3. `PubkeyProtocolUiCommunityUpdateForm`

Example (using React):
```
jsx
import React from 'react';

const CommunityHeader = ({ community }) => {
    return (
        <div>
            {/* existing fields... */}
            <p>Description: {community.description}</p>
        </div>
    );
};
```

*Step 6: Update CLI sample data*

In your CLI code, add the `description` field to the sample data:

```
let mut community = Community {
    // existing fields...
    description: Some("Sample Description".to_string()),
};
```

*Step 7: Commit and push changes*

```
bash
git add .
git commit -m "Add community description field"
git push origin username/add-community-description
```

*Step 8: Create PR against next branch*

Create a pull request on GitHub:

Title: Add community description field

Body:

This PR adds an optional description field to the Community struct, allowing authorities to update it using the community_update instruction.

*Step 9: Verify GitHub Actions tests*

Ensure all tests pass.




