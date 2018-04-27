# labprog
Lab prog
package com.customer.delight.labprograms;

import android.Manifest;
import android.content.Context;
import android.content.IntentSender;
import android.content.pm.PackageManager;
import android.database.Cursor;
import android.location.Criteria;
import android.location.Location;
import android.location.LocationListener;
import android.location.LocationManager;
import android.provider.ContactsContract;
import android.support.v4.app.ActivityCompat;
import android.support.v4.widget.SimpleCursorAdapter;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.Menu;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;



public class Main2Activity extends AppCompatActivity implements LocationListener {
    LocationManager locationManager;
    String provider;
    ListView lv;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main2);

        lv = (ListView)findViewById(R.id.list);

        getContacts();

        locationManager = (LocationManager) getSystemService(Context.LOCATION_SERVICE);
        Criteria criteria = new Criteria();
        provider = locationManager.getBestProvider(criteria, false);
        if (provider != null && !provider.equals("")) {
            if (ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED && ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_COARSE_LOCATION) != PackageManager.PERMISSION_GRANTED) {

                return;
            }
            Location location = locationManager.getLastKnownLocation(provider);
            locationManager.requestLocationUpdates(provider, 20000, 1, this);
            if (location != null)
                onLocationChanged(location);
            else
                Toast.makeText(getBaseContext(), "Location cant be accessed", Toast.LENGTH_SHORT).show();
        }
        else{
            Toast.makeText(getBaseContext(),"No Provider Found",Toast.LENGTH_SHORT).show();
        }
    }
    public void onLocationChanged(Location location){
        TextView lon=(TextView)findViewById(R.id.lon);
        TextView lat=(TextView)findViewById(R.id.lat);
        lon.setText("Longitude:"+location.getLongitude());
        lat.setText("Lattitude:"+location.getLatitude());
    }

    @Override
    public void onStatusChanged(String s, int i, Bundle bundle) {

    }

    @Override
    public void onProviderEnabled(String s) {

    }

    @Override
    public void onProviderDisabled(String s) {

    }
     private void getContacts() {
        // Run query
        Cursor cursor =getContentResolver().query(ContactsContract.CommonDataKinds.Phone.CONTENT_URI,null,null,null,null);
        startManagingCursor(cursor);

        String[] from={ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME,ContactsContract.CommonDataKinds.Phone.NUMBER, ContactsContract.CommonDataKinds.Phone._ID};

        int[] to={android.R.id.text1,android.R.id.text2};

        SimpleCursorAdapter adapter =
                new SimpleCursorAdapter(this,
                        android.R.layout.simple_list_item_2,
                        cursor,
                        from,
                        to);
        lv.setAdapter(adapter);
        lv.setChoiceMode(ListView.CHOICE_MODE_MULTIPLE);
    }
}

//EMAIL
public void emaildb(){
        String to="nikhilbh1995@gmail.com";
        String sub=name.getText().toString();
        String msg=year.getText().toString();
        Intent email=new Intent(Intent.ACTION_SEND);
        email.putExtra(Intent.EXTRA_EMAIL,new String[]{to});
        email.putExtra(Intent.EXTRA_SUBJECT,sub);
        email.putExtra(Intent.EXTRA_TEXT,msg);
        email.setType("message/rfc822");
        startActivity(Intent.createChooser(email,"Select email/client"));

    }

  //SMS

    public void sendsms(){
            Uri uri=Uri.parse("smsto:"+ph.getText().toString());
            Intent Sms=new Intent(Intent.ACTION_SENDTO,uri);
            Sms.putExtra("sms_body",msg.getText().toString());
            try{
                startActivity(Sms);
            }
            catch(Exception ex){
                Toast.makeText(MainActivity.this,"MSG failed",Toast.LENGTH_LONG).show();
                ex.printStackTrace();
            }

//GPS
import android.app.Service;
import android.content.Context;
import android.content.Intent;
import android.location.Location;
import android.location.LocationListener;
import android.location.LocationManager;
import android.os.Bundle;
import android.os.IBinder;
import android.util.Log;

public class GPSTracker implements LocationListener {
private final Context mContext;
boolean isGPSEnabled = false;
boolean isNetworkEnabled = false;
boolean canGetLocation = false;
Location location = null; 
double latitude; 
double longitude; 

private static final long MIN_DISTANCE_CHANGE_FOR_UPDATES = 10; // 10 meters
private static final long MIN_TIME_BW_UPDATES = 1000 * 60 * 1; // 1 minute

protected LocationManager locationManager;
private Location m_Location;
 public GPSTracker(Context context) {
    this.mContext = context;
    m_Location = getLocation();
    System.out.println("location Latitude:"+m_Location.getLatitude());
    System.out.println("location Longitude:"+m_Location.getLongitude());
    System.out.println("getLocation():"+getLocation());
    }

public Location getLocation() {
    try {
        locationManager = (LocationManager) mContext
                .getSystemService(Context.LOCATION_SERVICE);

        isGPSEnabled = locationManager
                .isProviderEnabled(LocationManager.GPS_PROVIDER);

        isNetworkEnabled = locationManager
                .isProviderEnabled(LocationManager.NETWORK_PROVIDER);

        if (!isGPSEnabled && !isNetworkEnabled) {
            // no network provider is enabled
        } 
        else {
            this.canGetLocation = true;
            if (isNetworkEnabled) {
                locationManager.requestLocationUpdates(
                        LocationManager.NETWORK_PROVIDER,
                        MIN_TIME_BW_UPDATES,
                        MIN_DISTANCE_CHANGE_FOR_UPDATES, this);
                Log.d("Network", "Network Enabled");
                if (locationManager != null) {
                    location = locationManager
                            .getLastKnownLocation(LocationManager.NETWORK_PROVIDER);
                    if (location != null) {
                        latitude = location.getLatitude();
                        longitude = location.getLongitude();
                    }
                }
            }
            if (isGPSEnabled) {
                if (location == null) {
                    locationManager.requestLocationUpdates(
                            LocationManager.GPS_PROVIDER,
                            MIN_TIME_BW_UPDATES,
                            MIN_DISTANCE_CHANGE_FOR_UPDATES, this);
                    Log.d("GPS", "GPS Enabled");
                    if (locationManager != null) {
                        location = locationManager
                                .getLastKnownLocation(LocationManager.GPS_PROVIDER);
                        if (location != null) {
                            latitude = location.getLatitude();
                            longitude = location.getLongitude();
                        }
                    }
                }
            }
        }

    } catch (Exception e) {
        e.printStackTrace();
    }

    return location;
}

public void stopUsingGPS() {
    if (locationManager != null) {
        locationManager.removeUpdates(GPSTracker.this);
    }
}

public double getLatitude() {
    if (location != null) {
        latitude = location.getLatitude();
    }

    return latitude;
}

public double getLongitude() {
    if (location != null) {
        longitude = location.getLongitude();
    }

    return longitude;
}

public boolean canGetLocation() {
    return this.canGetLocation;
}

@Override
public void onLocationChanged(Location arg0) {
    // TODO Auto-generated method stub

}

@Override
public void onProviderDisabled(String arg0) {
    // TODO Auto-generated method stub

}

@Override
public void onProviderEnabled(String arg0) {
    // TODO Auto-generated method stub

}

@Override
public void onStatusChanged(String arg0, int arg1, Bundle arg2) {
    // TODO Auto-generated method stub

}


}


