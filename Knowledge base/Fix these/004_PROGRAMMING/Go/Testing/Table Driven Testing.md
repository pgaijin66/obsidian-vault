

During normal testing, there are a lot of duplicate code. For each code block, there will be a test with one input and expected output. Anything other than that is a boilerplate. 

In Table Driven Testing, we decide a structure to hold our test inputs and expected outputs. The tests structure is a usually a local declaration because we want to reuse this name for other tests in this package.

```go

type TestCase struct {
	value    int
	expected bool
	actual   bool
}

func TestCalculateIsAmstrong(t *testing.T) {

	t.Run("Check against all the 3 numbers", func(t *testing.T) {
		tests := []TestCase{
			TestCase{value: 153, expected: true},
			TestCase{value: 370, expected: true},
			TestCase{value: 371, expected: true},
			TestCase{value: 407, expected: true},
		}

		for _, test := range tests {
			t.Run("Test", func(t *testing.T) {
				actual := calculator.CalculateIsAmstrong(test.value)
				if test.expected != actual {
					t.Fail()
				}
			})
		}
	})

}
```


Note: You do not need to name the type as well, and can just use anonymous structs literal to reduce the boiler plate code

```go

func TestCalculateIsAmstrong(t *testing.T) {

	t.Run("Check against all the 3 numbers", func(t *testing.T) {

		// Tests table for table driven tests
		tests := []struct {
			value    int
			expected bool
		}{
			{value: 153, expected: true},
			{value: 370, expected: true},
			{value: 371, expected: true},
			{value: 407, expected: true},
		}

		for _, test := range tests {
			t.Run("Test", func(t *testing.T) {
				actual := calculator.CalculateIsAmstrong(test.value)
				if test.expected != actual {
					t.Fail()
				}
			})
		}
	})

}

```


Show test number by enumerating test cases using `reflect`

```go
func TestSplit(t *testing.T) {
    tests := []struct {
        input string
        sep   string
        want  []string
    }{
        {input: "a/b/c", sep: "/", want: []string{"a", "b", "c"}},
        {input: "a/b/c", sep: ",", want: []string{"a/b/c"}},
        {input: "abc", sep: "/", want: []string{"abc"}},
        {input: "a/b/c/", sep: "/", want: []string{"a", "b", "c"}},
    }

    for i, tc := range tests {
        got := Split(tc.input, tc.sep)
        if !reflect.DeepEqual(tc.want, got) {
            t.Fatalf("test %d: expected: %v, got: %v", i+1, tc.want, got)
        }
    }
}
```


DRY testing using map literal

```go
func TestClient_GetMemberID(t *testing.T) {
	tests := []struct {
		name    string
		fields  fields
		args    args
		want    uint64
		wantErr error
	}{
		{
			name: "member ID found",
			fields: fields{
				Endpoints: []string{},
				newEtcdClient: func(endpoints []string) (etcdClient, error) {
					f := &fakeEtcdClient{
						members: []*pb.Member{
							{
								ID:   1,
								Name: "member1",
								PeerURLs: []string{
									"https://member1:2380",
								},
							},
						},
					}
					return f, nil
				},
			},
			args: args{
				peerURL: "https://member1:2380",
			},
			wantErr: nil,
			want:    1,
		},
		{
			name: "member ID not found",
			fields: fields{
				Endpoints: []string{},
				newEtcdClient: func(endpoints []string) (etcdClient, error) {
					f := &fakeEtcdClient{
						members: []*pb.Member{
							{
								ID:   1,
								Name: "member1",
								PeerURLs: []string{
									"https://member1:2380",
								},
							},
						},
					}
					return f, nil
				},
			},
			args: args{
				peerURL: "https://member2:2380",
			},
			wantErr: ErrNoMemberIDForPeerURL,
			want:    0,
		},
	}
	
	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			c := &Client{
				Endpoints:     tt.fields.Endpoints,
				newEtcdClient: tt.fields.newEtcdClient,
			}

			got, err := c.GetMemberID(tt.args.peerURL)
			
			if !errors.Is(tt.wantErr, err) {
				t.Errorf("Client.GetMemberID() error = %v, wantErr %v", err, tt.wantErr)
				return
			}
			
			if got != tt.want {
				t.Errorf("Client.GetMemberID() = %v, want %v", got, tt.want)
			}
		})
	}
}
```