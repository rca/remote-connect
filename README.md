# remote-connect

This is a small script that can be run on a Mac to give an administrator access to a Mac that's not on the local network, nor is accessible over the Internet (i.e. it's on a private network).

This requires two things:

- The Mac has Remote Login enabled in the System Preferences under Sharing.  If that is not enabled, this will not work

- You have access to a machine that is SSH'able over the Internet.  This machine will be used as a tunnel to gain access to the Mac's SSH connection.


## installation

The following command can be run in a Terminal to install the tunnel, where `<ssh hostname>` can be something like `username@ssh_host`:

```
curl -s -L 'https://raw.githubusercontent.com/rca/remote-connect/master/remote-connect' > /tmp/remote-connect && bash /tmp/remote-connect --vnc-port 59000 --ssh-port 22000 --host <ssh hostname>
```

Note: to use this to remotely access multiple Macs, give each one a unique SSH and VNC port.  for example:

- Mac 0: vnc port `59000`, ssh port `22000`
- Mac 1: vnc port `59001`, ssh port `22001`
- etc

The command will produce output like this:

```
initialize...
copy and paste this to your administrator
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDPi6buKh79RzyaLknt1GdQiolOUKcAey2i2xTPbq3/MDQy3PRvCafX080saqL8q2q/lJiayk2N7XXGR7oBy1d4tlsR89yAQQpMyVjaTwBgdP6hILe0JbjS+7PYaYmkuExWbNQz39u1gP5B5z/rHFFjqJ8tT7rXv0nQoGTYGoSf4tpcKhFAz5JYe43s7EHStkacxmXBUexoa2kNwu0i7o4TcEanI9qKZBMw7n1MRNhjEAxaxD6vil8yCAjKWAZRs9gWLU5sR4B03fYitCH5Bc+4XsYM2Ub85QARvhZ3Yq/bRHKuuzEnIwUb4jGPyo0GbOnNt6hbY/WTcO0FJryqqmqypkEf32J0+NlbJbAuq2CiEsOoTL4EWpQIoPwSvJC7LHoTi5dXb7nrhci+T29vIEvug+4lVEbsaX7GQd+sp6rfaTE2xgrbVTTkF4xOvPowAUONGvWbanRZeFCvfXNZ4OmE8qfur4mNDDrqQ0nO/7RYNOTbS5MO509913FkYbTlvct8RHHtvGMYXJl9juu744IWOHZcudWEucsLm4Xj+X3peVZxUu7ZONT1L7ewEY68yYxs1VE0LhEMB912NVaSKUR/gCr2W0ACY81vy/9HrUr+wItW6LXHiCDlHIFlpgmajv//815/unuW2rMM37GSmmX17eXJlCDR+/0P71DUM2qMfQ== berto@mymac
```

Have the remote user copy and paste the information above into text / slack / email / postal service.

On the ssh host, place the public key above into `~username/.ssh/authorized_keys`.  The key can be prefixed with `restrict,port-forwarding` in order to prevent the key from being used to gain shell access to the ssh host.  For example:

```
restrict,port-forwarding ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDPi6buKh79RzyaLknt1GdQiolOUKcAey2i2xTPbq3/MDQy3PRvCafX080saqL8q2q/lJiayk2N7XXGR7oBy1d4tlsR89yAQQpMyVjaTwBgdP6hILe0JbjS+7PYaYmkuExWbNQz39u1gP5B5z/rHFFjqJ8tT7rXv0nQoGTYGoSf4tpcKhFAz5JYe43s7EHStkacxmXBUexoa2kNwu0i7o4TcEanI9qKZBMw7n1MRNhjEAxaxD6vil8yCAjKWAZRs9gWLU5sR4B03fYitCH5Bc+4XsYM2Ub85QARvhZ3Yq/bRHKuuzEnIwUb4jGPyo0GbOnNt6hbY/WTcO0FJryqqmqypkEf32J0+NlbJbAuq2CiEsOoTL4EWpQIoPwSvJC7LHoTi5dXb7nrhci+T29vIEvug+4lVEbsaX7GQd+sp6rfaTE2xgrbVTTkF4xOvPowAUONGvWbanRZeFCvfXNZ4OmE8qfur4mNDDrqQ0nO/7RYNOTbS5MO509913FkYbTlvct8RHHtvGMYXJl9juu744IWOHZcudWEucsLm4Xj+X3peVZxUu7ZONT1L7ewEY68yYxs1VE0LhEMB912NVaSKUR/gCr2W0ACY81vy/9HrUr+wItW6LXHiCDlHIFlpgmajv//815/unuW2rMM37GSmmX17eXJlCDR+/0P71DUM2qMfQ== berto@mymac
```

The mac will try to connect to the SSH host every 10 seconds.  Once the remote host has established a connection, the administrator can also ssh to the SSH host to make the machine available on local ports:

```
ssh -N -L 10022:localhost:22000 -L 15900:localhost:59000 username@ssh_host
```

With that command running, the remote Mac is accessible on the local Mac via SSH port `10022` and VNC port `15900`.
