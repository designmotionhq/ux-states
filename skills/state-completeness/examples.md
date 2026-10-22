# state-completeness: a list with every state it can be in

## Example

WRONG (happy path only):
```jsx
function UsersList() {
  const { data } = useQuery('users');
  return (
    <ul>
      {data?.map(user => <li>{user.name}</li>)}
    </ul>
  );
}
```

RIGHT (all states):
```jsx
function UsersList() {
  const { data, isLoading, isError, error, isFetching } = useQuery('users');
  const isOffline = useOnlineStatus() === false;

  if (isOffline && !data) {
    return <OfflineState onRetry={refetch} />;
  }

  if (isLoading) {
    return <UsersListSkeleton rows={5} />;
  }

  if (isError) {
    return (
      <ErrorState
        title="We couldn't load users"
        message={error.userMessage ?? "Your connection might be unstable."}
        onRetry={refetch}
      />
    );
  }

  if (data?.length === 0) {
    return (
      <EmptyState
        title="No users yet"
        body="Invite your first teammate to get started."
        action={<Button onClick={onInvite}>Invite a user</Button>}
      />
    );
  }

  return (
    <>
      {isFetching && <PartialUpdateBanner />}
      <ul>
        {data.map(user => <UserRow key={user.id} user={user} />)}
      </ul>
    </>
  );
}
```
